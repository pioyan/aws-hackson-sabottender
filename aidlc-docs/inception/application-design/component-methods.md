# コンポーネントメソッド設計

## 前提

- インターフェース粒度は中間粒度を採用する。
- 主要ユースケースを起点にしつつ、補助操作も明示する。
- 開始前画面は初回起動と継続利用で異なる開始導線を持つが、どちらも同じ開始ユースケースへ変換する。
- ここで定義するのは高レベルのシグネチャであり、詳細ロジックは Functional Design で詰める。

## Presentation コンポーネント

### SessionSetupScreenComponent

| メソッド | 目的 | 入力 | 出力 |
| --- | --- | --- | --- |
| `buildSessionStartRequest(input: SetupInput): SessionStartRequest` | 設定変更後の入力をセッション開始要求へ変換する | `SetupInput` | `SessionStartRequest` |
| `renderSetupState(state: SetupUiState): Unit` | 開始前設定画面を描画する | `SetupUiState` | `Unit` |
| `loadInitialSelections(): SetupUiState` | 前回設定と初回起動判定を反映した開始画面状態を作る | なし | `SetupUiState` |
| `buildQuickStartRequest(state: SetupUiState): SessionStartRequest` | 復元済み設定から即開始用の要求を作る | `SetupUiState` | `SessionStartRequest` |

### PlaybackScreenComponent

| メソッド | 目的 | 入力 | 出力 |
| --- | --- | --- | --- |
| `renderPlaybackState(state: PlaybackUiState): Unit` | 再生中の主画面を描画する | `PlaybackUiState` | `Unit` |
| `dispatchPlaybackIntent(intent: PlaybackIntent): Unit` | 画面からの操作イベントを上位へ渡す | `PlaybackIntent` | `Unit` |
| `showOverlayMenu(model: OverlayMenuModel): Unit` | オーバーレイメニューを表示する | `OverlayMenuModel` | `Unit` |

### OverlayMenuComponent

| メソッド | 目的 | 入力 | 出力 |
| --- | --- | --- | --- |
| `buildMenuAction(input: OverlayMenuInput): OverlayMenuAction` | メニュー入力をアクションへ変換する | `OverlayMenuInput` | `OverlayMenuAction` |
| `renderMenuState(state: OverlayMenuState): Unit` | メニュー状態を描画する | `OverlayMenuState` | `Unit` |

## Control / Application コンポーネント

### SessionController

| メソッド | 目的 | 入力 | 出力 |
| --- | --- | --- | --- |
| `startSession(request: SessionStartRequest): SessionHandle` | セッションを開始する | `SessionStartRequest` | `SessionHandle` |
| `advanceSession(handle: SessionHandle): SessionStepResult` | 次のお題へ進行する | `SessionHandle` | `SessionStepResult` |
| `applyMenuAction(handle: SessionHandle, action: OverlayMenuAction): SessionUpdate` | 再生中のメニュー操作を反映する | `SessionHandle`, `OverlayMenuAction` | `SessionUpdate` |
| `stopSession(handle: SessionHandle, reason: SessionEndReason): SessionSummary` | セッションを終了する | `SessionHandle`, `SessionEndReason` | `SessionSummary` |

### SessionTerminationPolicy

| メソッド | 目的 | 入力 | 出力 |
| --- | --- | --- | --- |
| `startTimer(duration: SessionDuration): TimerToken` | タイマー終了条件を開始する | `SessionDuration` | `TimerToken` |
| `evaluateTermination(event: SessionEvent): SessionEndDecision` | セッション終了判定を返す | `SessionEvent` | `SessionEndDecision` |
| `cancelTimer(token: TimerToken): Unit` | 終了タイマーを停止する | `TimerToken` | `Unit` |

### ContentCoordinator

| メソッド | 目的 | 入力 | 出力 |
| --- | --- | --- | --- |
| `prepareSequence(request: SessionStartRequest): PromptSequence` | セッション開始時の初期シーケンスを用意する | `SessionStartRequest` | `PromptSequence` |
| `resolveNextPrompt(context: SessionProgressContext): PromptBundle` | 次のお題を返す | `SessionProgressContext` | `PromptBundle` |
| `refreshThemeIfNeeded(context: SessionProgressContext): ThemeUpdate` | 情景テーマの継続性を維持する | `SessionProgressContext` | `ThemeUpdate` |

## State / Persistence コンポーネント

### SettingsStore

| メソッド | 目的 | 入力 | 出力 |
| --- | --- | --- | --- |
| `loadPreferences(): UserPreferences` | ユーザー設定を復元する | なし | `UserPreferences` |
| `savePreferences(preferences: UserPreferences): Unit` | ユーザー設定を保存する | `UserPreferences` | `Unit` |
| `saveLastSessionDefaults(defaults: SessionDefaults): Unit` | 次回起動時の初期値を保存する | `SessionDefaults` | `Unit` |

### SessionContextStore

| メソッド | 目的 | 入力 | 出力 |
| --- | --- | --- | --- |
| `beginContext(metadata: SessionContextMetadata): SessionContextId` | セッション文脈の保存を開始する | `SessionContextMetadata` | `SessionContextId` |
| `appendPromptContext(contextId: SessionContextId, item: PromptContextItem): Unit` | お題ごとの識別情報を追加する | `SessionContextId`, `PromptContextItem` | `Unit` |
| `finalizeContext(contextId: SessionContextId, summary: SessionSummary): Unit` | セッション終了時に文脈を確定する | `SessionContextId`, `SessionSummary` | `Unit` |

## Audio コンポーネント

### BgmPlaybackComponent

| メソッド | 目的 | 入力 | 出力 |
| --- | --- | --- | --- |
| `start(track: BgmTrack, volume: VolumeLevel): Unit` | BGM を開始する | `BgmTrack`, `VolumeLevel` | `Unit` |
| `switchTrack(track: BgmTrack): Unit` | BGM を切り替える | `BgmTrack` | `Unit` |
| `setVolume(level: VolumeLevel): Unit` | ユーザー操作による音量変更を反映する | `VolumeLevel` | `Unit` |
| `applyFade(progress: SessionFadeProgress): Unit` | 進行に応じたフェードを適用する | `SessionFadeProgress` | `Unit` |
| `stop(): Unit` | BGM を停止する | なし | `Unit` |

### NarrationPlaybackComponent

| メソッド | 目的 | 入力 | 出力 |
| --- | --- | --- | --- |
| `playCue(cue: NarrationCue): CueHandle` | 読み上げキューを再生する | `NarrationCue` | `CueHandle` |
| `playAnswer(answer: AnswerCue): CueHandle` | 正解読み上げを再生する | `AnswerCue` | `CueHandle` |
| `playSilence(duration: SilenceDuration): Unit` | 無音区間を再生する | `SilenceDuration` | `Unit` |
| `stopCurrentCue(): Unit` | 現在の読み上げを停止する | なし | `Unit` |

## External Adapter コンポーネント

### FixedContentSource

| メソッド | 目的 | 入力 | 出力 |
| --- | --- | --- | --- |
| `loadThemeCandidates(criteria: ThemeCriteria): List<ThemeSeed>` | 固定テーマ候補を取得する | `ThemeCriteria` | `List<ThemeSeed>` |
| `loadPrompt(seed: PromptSeed): PromptBundle` | 固定お題を取得する | `PromptSeed` | `PromptBundle` |

### BedrockContentGateway

| メソッド | 目的 | 入力 | 出力 |
| --- | --- | --- | --- |
| `generatePrompts(request: BedrockPromptRequest): GeneratedPromptBatch` | Bedrock を用いてお題を生成する | `BedrockPromptRequest` | `GeneratedPromptBatch` |
| `validateGeneration(response: GeneratedPromptBatch): ValidationResult` | 生成結果の体験要件適合性を判定する | `GeneratedPromptBatch` | `ValidationResult` |

### VoiceSynthesisGateway

| メソッド | 目的 | 入力 | 出力 |
| --- | --- | --- | --- |
| `synthesizeNarration(request: NarrationSynthesisRequest): AudioAsset` | 自前音声生成サーバーへ音声生成を依頼する | `NarrationSynthesisRequest` | `AudioAsset` |
| `getHealth(): IntegrationHealth` | 音声生成接続先の健全性を確認する | なし | `IntegrationHealth` |
| `resolveDeploymentProfile(): VoiceRuntimeProfile` | ローカル Docker / 将来 ECS の接続プロファイルを選ぶ | なし | `VoiceRuntimeProfile` |
