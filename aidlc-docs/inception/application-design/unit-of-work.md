# Unit of Work 定義

## 分解サマリー

- Greenfield の Android ネイティブアプリとして、multi-unit monolith を採用する。
- unit 粒度は、4 から 5 unit 程度の大きめモジュールとし、ユーザーフロー起点の理解しやすさと共通技術責務の再利用を両立する。
- story grouping はハイブリッドとし、開始導線や再生体験などのユーザーフローを基準にしつつ、設定 / 文脈保持のような横断責務は独立 unit にまとめる。
- 外部連携は独立 integration unit に切り出さず、Content / Audio unit の内部 adapter として保持し、将来の切り出し余地は API 契約で確保する。

## Unit 一覧

| Unit ID | Unit 名 | 主目的 | 主に含む設計要素 | 主対象ストーリー | 想定コード配置 |
| --- | --- | --- | --- | --- | --- |
| UOW-01 | Experience Surface | 開始導線と再生中 UI の体験面を担う | SessionSetupScreenComponent, PlaybackScreenComponent, OverlayMenuComponent | US-01, US-05 | src/experience-surface/ |
| UOW-02 | Session Flow | セッションの開始、進行、終了を統括する | SessionApplicationService, SessionController, SessionTerminationPolicy | US-04 | src/session-flow/ |
| UOW-03 | Content Delivery | 情景テーマに沿ったお題供給を担う | ContentOrchestrationService, ContentCoordinator, FixedContentSource, BedrockContentGateway | US-02 | src/content-delivery/ |
| UOW-04 | Audio Experience | 読み上げと BGM の再生体験を担う | AudioOrchestrationService, BgmPlaybackComponent, NarrationPlaybackComponent, VoiceSynthesisGateway | US-03, US-06 | src/audio-experience/ |
| UOW-05 | Settings and Context | 設定復元と将来パーソナライズ向け文脈保持を担う | SetupConfigurationService, SettingsStore, SessionContextService, SessionContextStore | US-07 | src/settings-context/ |

## Unit 詳細

### UOW-01 Experience Surface

- 目的: 初回起動時の設定入力、継続利用時の即開始、再生中画面、オーバーレイメニューを一貫した低刺激 UI として提供する。
- 含める責務:
  - 開始画面の表示と入力受付
  - 前回設定の要約表示と主要開始操作の強調
  - 再生中の暗い画面と状態表示
  - オーバーレイメニューの表示と操作イベント送出
- 含めない責務:
  - セッション進行の判定
  - オーディオ再生の順序制御
  - 設定や文脈の永続化ロジック
- 主要契約:
  - Session Flow へ SessionStartRequest と PlaybackIntent を渡す
  - Settings and Context から開始画面状態とメニュー既定値を受け取る

### UOW-02 Session Flow

- 目的: セッション開始、次のお題への進行、終了条件評価、unit 間のオーケストレーションを担う。
- 含める責務:
  - セッションライフサイクルの開始 / 進行 / 終了
  - Content Delivery、Audio Experience、Settings and Context の呼び出し順序管理
  - タイマー終了条件の起動と評価
  - UI から渡された menu action の反映
- 含めない責務:
  - お題生成そのもの
  - 音声や BGM の低レベル再生
  - 画面描画
- 主要契約:
  - Content Delivery から prompt bundle を受け取る
  - Audio Experience へ cue 再生を依頼する
  - Settings and Context へ defaults 保存と context 記録を依頼する

### UOW-03 Content Delivery

- 目的: 固定コンテンツと Bedrock 生成コンテンツを切り替えつつ、連続した情景テーマを維持したお題供給を担う。
- 含める責務:
  - テーマ候補の選択と継続性維持
  - 固定 / AI 生成コンテンツの切り替え
  - セッション後半に向けた短文化・抽象化調整
  - Bedrock adapter の契約管理
- 含めない責務:
  - セッション終了判定
  - 音声合成や再生制御
  - 設定保存
- 主要契約:
  - Session Flow からジャンル、モード、進行文脈を受け取る
  - 固定 prompt と generated prompt を同一の prompt bundle 契約で返す

### UOW-04 Audio Experience

- 目的: 特徴説明、無音、正解読み上げ、BGM フェードを含む睡眠導入向けの音体験を担う。
- 含める責務:
  - 読み上げ順序の管理
  - BGM の再生、切り替え、音量調整、フェード制御
  - 音声生成サーバー adapter の契約管理
  - UI menu action を安全な音声制御へ変換
- 含めない責務:
  - セッション全体の進行判定
  - prompt 内容の生成
  - ユーザー設定の永続化
- 主要契約:
  - Session Flow から narration cue と再生命令を受け取る
  - VoiceSynthesisGateway で音声アセットを取得する

### UOW-05 Settings and Context

- 目的: 前回設定の復元、再生中設定の保存、将来パーソナライズに使うセッション文脈の保持を担う。
- 含める責務:
  - 初回起動 / 継続利用の判定
  - ジャンル、モード、BGM、音量の保存と復元
  - セッション開始時と各お題ごとの context 記録
  - 将来の推薦へ接続できる識別情報の保存
- 含めない責務:
  - UI の表示制御
  - prompt 生成
  - 音声再生
- 主要契約:
  - Experience Surface へ SetupUiState を返す
  - Session Flow から保存イベントを受けて defaults と context を永続化する

## コード配置戦略

Greenfield multi-unit monolith として、unit ごとにトップレベルディレクトリを分け、その中で必要な layer を持たせる。

```text
src/
  experience-surface/
    presentation/
    application/
    domain/
    data/
  session-flow/
    application/
    domain/
    data/
  content-delivery/
    application/
    domain/
    data/
  audio-experience/
    application/
    domain/
    data/
  settings-context/
    application/
    domain/
    data/
tests/
  experience-surface/
  session-flow/
  content-delivery/
  audio-experience/
  settings-context/
```

- presentation は UI surface を持つ unit のみ必須とする。
- application は unit のユースケース入口や orchestration を置く。
- domain は unit 固有のルール、モデル、契約を置く。
- data は gateway、store、adapter、repository などの実装寄り要素を置く。

## 推奨実装順序

1. UOW-05 Settings and Context
2. UOW-03 Content Delivery と UOW-04 Audio Experience を並行着手
3. UOW-02 Session Flow
4. UOW-01 Experience Surface

## 境界検証メモ

- unit 数は 5 であり、Q1 の「4 から 5 unit 程度」に一致する。
- Experience Surface と Settings and Context は密接に連携するが、UI と保持責務を分離しているため、Q2 / Q6 の方針に整合する。
- Bedrock と音声生成サーバーはそれぞれ Content / Audio unit に内包し、Q5 の方針どおり独立 integration unit にはしていない。
- すべての unit は SessionApplicationService を中心とする上位オーケストレーションに接続でき、Q3 の依存方針に整合する。