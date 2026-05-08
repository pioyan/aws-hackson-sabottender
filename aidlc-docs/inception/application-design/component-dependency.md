# コンポーネント依存関係

## 依存関係マトリクス

| 依存元 | 依存先 | 依存種別 | 理由 |
| --- | --- | --- | --- |
| SessionSetupScreenComponent | SetupConfigurationService | 同期呼び出し | 前回設定の復元と開始要求生成のため |
| SessionSetupScreenComponent | SessionApplicationService | 同期呼び出し | セッション開始要求を渡すため |
| PlaybackScreenComponent | SessionApplicationService | イベント通知 | 再生中の操作と終了要求を渡すため |
| PlaybackScreenComponent | SetupConfigurationService | 同期呼び出し | メニュー表示時の設定値取得のため |
| OverlayMenuComponent | SessionApplicationService | イベント通知 | BGM、音量、モード変更を反映するため |
| SessionApplicationService | SessionController | 同期呼び出し | セッション進行を制御するため |
| SessionApplicationService | ContentOrchestrationService | 同期呼び出し | 次のお題取得のため |
| SessionApplicationService | AudioOrchestrationService | 同期呼び出し | 再生順序と音量制御を依頼するため |
| SessionApplicationService | SessionContextService | 同期呼び出し | セッション文脈を記録するため |
| SessionController | SessionTerminationPolicy | 同期呼び出し | タイマー終了判定のため |
| ContentOrchestrationService | ContentCoordinator | 同期呼び出し | テーマ継続とシーケンス管理のため |
| ContentOrchestrationService | FixedContentSource | 同期呼び出し | 固定コンテンツ取得のため |
| ContentOrchestrationService | BedrockContentGateway | 同期呼び出し | AI 生成コンテンツ取得のため |
| AudioOrchestrationService | NarrationPlaybackComponent | 同期呼び出し | 読み上げキュー再生のため |
| AudioOrchestrationService | BgmPlaybackComponent | 同期呼び出し | BGM 再生とフェード制御のため |
| AudioOrchestrationService | VoiceSynthesisGateway | 同期呼び出し | 音声アセット生成のため |
| SessionContextService | SessionContextStore | 同期呼び出し | セッション文脈保存のため |
| SessionContextService | SettingsStore | 同期呼び出し | モードや設定値との関連付けのため |
| SetupConfigurationService | SettingsStore | 同期呼び出し | 設定の復元と保存のため |

## 通信パターン

- Presentation からサービス層へはユーザー意図ベースのイベント送信を行う。
- サービス層からコンポーネント / adapter へは同期呼び出しを基本とする。
- オーディオ進行は AudioOrchestrationService が順序制御し、UI に直接依存させない。
- Bedrock と音声生成サーバーは、それぞれ専用 gateway を通じて呼び出す。

## データフロー図（テキスト）

### 1. セッション開始フロー

```text
SessionSetupScreenComponent
  -> SetupConfigurationService
  -> SessionApplicationService
  -> SessionController
  -> ContentOrchestrationService
  -> FixedContentSource / BedrockContentGateway
  -> SessionContextService
  -> AudioOrchestrationService
  -> VoiceSynthesisGateway
  -> NarrationPlaybackComponent + BgmPlaybackComponent
  -> PlaybackScreenComponent
```

### 2. お題進行フロー

```text
NarrationPlaybackComponent (特徴説明)
  -> AudioOrchestrationService
  -> SessionApplicationService
  -> SessionController.advanceSession()
  -> ContentOrchestrationService.resolveNextPrompt()
  -> SessionContextService.appendPromptContext()
  -> AudioOrchestrationService
  -> NarrationPlaybackComponent (無音 / 正解 / 次のお題)
```

### 3. セッション終了フロー

```text
SessionTerminationPolicy.startTimer()
  -> TimerElapsed
  -> SessionController
  -> SessionApplicationService.stopSession()
  -> AudioOrchestrationService.stopAll()
  -> SessionContextService.finalizeContext()
  -> PlaybackScreenComponent
```

## 将来拡張ポイント

- SessionTerminationPolicy に睡眠検知用インターフェースを追加できる。
- VoiceSynthesisGateway の接続先をローカル Docker から ECS へ差し替えできる。
- BedrockContentGateway の前段にキャッシュや再試行制御を追加できる。
