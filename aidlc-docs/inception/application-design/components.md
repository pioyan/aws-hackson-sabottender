# コンポーネント定義

## 設計サマリー

- Android クライアント内は、画面、セッション制御、オーディオ再生、コンテンツ取得、設定保持を分離する。
- 開始前画面は、初回起動時の設定入力と継続利用時の即開始を同じ画面で扱い、主要な開始操作を最優先表示とする。
- AI 生成コンテンツ取得は Bedrock 連携用ゲートウェイ、音声生成は独立した音声生成ゲートウェイとして分離する。
- 音声制御は UI から直接行わず、BGM と読み上げを別コンポーネントに分けて上位サービスで協調させる。
- 初期版の終了条件はタイマー中心で設計し、睡眠検知は将来拡張用の差し込みポイントとして扱う。
- 将来のパーソナライズに必要な識別情報は、セッション制御と設定保持の責務として扱う。

## コンポーネント一覧

| コンポーネント | 区分 | 目的 | 主責務 | 公開インターフェースの例 |
| --- | --- | --- | --- | --- |
| SessionSetupScreenComponent | Presentation | 開始前設定の入力と即開始導線を担う | 初回起動時の設定入力、前回設定の要約表示、主要開始操作と補助的な設定変更導線の提示 | `render(state)` `buildQuickStartRequest()` `submitStart(request)` |
| PlaybackScreenComponent | Presentation | 再生中の主画面を担う | 暗い画面の維持、再生状態表示、操作イベント送出 | `renderPlayback(state)` `dispatchMenuAction(action)` |
| OverlayMenuComponent | Presentation | 再生中メニューを担う | BGM、音量、モード変更などの入力をまとめる | `showMenu(model)` `emitMenuAction(action)` |
| SessionController | Control | セッション全体の進行を管理する | 開始、次のお題への進行、終了条件評価、セッション文脈管理の起点 | `startSession(request)` `advanceSession()` `stopSession(reason)` |
| SessionTerminationPolicy | Control | 初期版の終了条件を管理する | タイマー起動、タイマー終了判定、将来の睡眠検知差し込み点の保持 | `startTimer(duration)` `evaluateTermination(event)` |
| ContentCoordinator | Application | お題の取得と並び順制御を担う | 固定コンテンツと Bedrock 生成コンテンツの切り替え、情景テーマ継続、後半の簡素化制御 | `prepareSequence(request)` `resolveNextPrompt(context)` |
| SettingsStore | State/Persistence | ユーザー設定を保持する | 開始前設定、再生モード、BGM 選択、音量などの保存と復元 | `loadPreferences()` `savePreferences(preferences)` |
| SessionContextStore | State/Persistence | セッション文脈を保持する | コンテンツ種別、モード、ジャンル、再生条件など将来拡張用メタデータの保存 | `beginContext(metadata)` `appendPromptContext(item)` `finalizeContext(summary)` |
| BgmPlaybackComponent | Audio | BGM 再生を担う | BGM の再生、切り替え、音量変更、フェード適用 | `start(track, volume)` `setVolume(level)` `applyFade(progress)` `stop()` |
| NarrationPlaybackComponent | Audio | 読み上げ音声再生を担う | 特徴説明、無音、答え読み上げの再生進行 | `playCue(cue)` `stopCue()` |
| FixedContentSource | Content Adapter | 固定コンテンツの取得を担う | 固定お題、テーマ候補、ジャンル別素材の提供 | `loadThemeCandidates(criteria)` `loadPrompt(promptId)` |
| BedrockContentGateway | External Adapter | Bedrock を利用した AI 生成を担う | AI 生成リクエスト送信、生成レスポンス受信、生成エラーの境界化 | `generatePrompts(request)` |
| VoiceSynthesisGateway | External Adapter | 自前音声生成サーバー連携を担う | 音声生成依頼、生成結果取得、ローカル Docker から将来 ECS へ移行可能な接続抽象化 | `synthesizeNarration(request)` `getHealth()` |

## 主要な責務境界

### Presentation 層

- 画面は入力の収集と表示に集中し、Bedrock や音声生成サーバーを直接呼ばない。
- 画面は BGM と読み上げの制御を直接持たず、上位サービスへイベントを送る。

### Control / Application 層

- SessionController がセッション全体の流れを管理する。
- ContentCoordinator がコンテンツ選択とテーマ継続性を担保する。
- SessionTerminationPolicy が初期版ではタイマー中心の終了条件を提供する。

### Persistence 層

- SettingsStore はユーザー設定の保持を担当する。
- SessionContextStore は将来パーソナライズに向けた識別情報の保持を担当する。

### External Adapter 層

- BedrockContentGateway は AI 生成コンテンツ取得だけを扱う。
- VoiceSynthesisGateway は音声生成だけを扱い、デプロイ先の違いを内部へ漏らさない。

## 先送りする設計論点

- 睡眠検知の実装方式は今回の Application Design では確定しない。
- 音声生成サーバーのローカル Docker 実行から ECS 移行する際の詳細構成は Infrastructure Design で扱う。
