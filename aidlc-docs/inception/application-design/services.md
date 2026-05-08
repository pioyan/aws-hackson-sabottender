# サービス設計

## サービス一覧

| サービス | 役割 | 主な依存先 | 戻り先 |
| --- | --- | --- | --- |
| SessionApplicationService | セッション開始、進行、終了を統括する | SessionController, ContentOrchestrationService, AudioOrchestrationService, SessionContextService, SetupConfigurationService | Presentation 層 |
| ContentOrchestrationService | 固定 / AI 生成コンテンツを切り替えつつ、連続した情景テーマを維持する | ContentCoordinator, FixedContentSource, BedrockContentGateway | SessionApplicationService |
| AudioOrchestrationService | BGM と読み上げを協調させ、再生順序と音量バランスを管理する | BgmPlaybackComponent, NarrationPlaybackComponent, VoiceSynthesisGateway | SessionApplicationService |
| SessionContextService | 将来パーソナライズ用のメタデータ保存を管理する | SessionContextStore, SettingsStore | SessionApplicationService |
| SetupConfigurationService | 開始前設定と再生中設定の保存・復元を扱う | SettingsStore | Presentation 層, SessionApplicationService |

## サービス責務の詳細

### SessionApplicationService

- セッション開始要求を受け取り、開始前設定を確定する。
- ContentOrchestrationService から初期シーケンスを受け取り、AudioOrchestrationService に再生を依頼する。
- SessionTerminationPolicy の結果を受けてセッション継続または終了を判断する。
- SessionContextService へ文脈情報の保存を依頼する。

### ContentOrchestrationService

- 固定コンテンツと Bedrock 生成コンテンツの選択方針を管理する。
- 1 セッション内の情景テーマ継続を担保する。
- セッション後半ほど短く抽象的にするためのコンテンツ粒度を調整する。
- 生成結果が体験方針から外れた場合の再取得や差し替えを判断する。

### AudioOrchestrationService

- 読み上げキューを VoiceSynthesisGateway から取得し、NarrationPlaybackComponent へ渡す。
- BgmPlaybackComponent の再生状態と音量フェードを制御する。
- 無音時間、正解読み上げ、BGM 音量のバランスを一貫した順序で管理する。
- UI からのメニュー操作を安全な音声制御へ変換する。

### SessionContextService

- セッション開始時にコンテンツ種別、モード、ジャンルなどのメタデータ保存を開始する。
- 各お題ごとの source type、テーマ情報、再生条件を追記する。
- 初期版では識別情報保持に留め、推薦ロジック自体は扱わない。

### SetupConfigurationService

- 前回使用したジャンル、モード、BGM、音量の既定値を復元する。
- セッション開始前の選択内容を保存する。
- 再生中メニューからの変更を永続化対象へ変換する。

## オーケストレーション方針

- Presentation 層はサービスへ意図を伝えるだけに留める。
- SessionApplicationService がユースケース単位の入口になる。
- Bedrock と音声生成サーバーはそれぞれ別の adapter を通し、サービス層で必要時に協調させる。
- オーディオ再生は UI ではなく AudioOrchestrationService が責任を持つ。

## デプロイ前提の扱い

- Bedrock 連携は外部 AI コンテンツ取得アダプタとして扱う。
- 音声生成サーバーは現時点ではローカル Docker 配置を前提としつつ、VoiceSynthesisGateway の接続抽象化により ECS 移行余地を残す。
- AWS 認証や接続先切り替えの詳細は Infrastructure Design で具体化する。
