# Unit of Work 依存関係

## 依存関係の原則

- unit 間の依存は、Session Flow を中心とした上位オーケストレーションで管理する。
- Content Delivery、Audio Experience、Settings and Context は、独立した基盤 unit として先に整える。
- Experience Surface は Session Flow と Settings and Context の契約が安定してから統合する。
- 外部連携 adapter は各 unit 内部に閉じ込め、unit 間では adapter 実装ではなく契約を参照する。

## 依存関係マトリクス

| Unit | 依存先 | 依存種別 | 理由 |
| --- | --- | --- | --- |
| UOW-01 Experience Surface | UOW-02 Session Flow | 同期呼び出し / イベント送信 | セッション開始、再生中操作、画面状態更新のため |
| UOW-01 Experience Surface | UOW-05 Settings and Context | 同期呼び出し | 初期表示、前回設定復元、メニュー既定値取得のため |
| UOW-02 Session Flow | UOW-03 Content Delivery | 同期呼び出し | 情景テーマに沿った prompt 取得のため |
| UOW-02 Session Flow | UOW-04 Audio Experience | 同期呼び出し | 読み上げ順序と BGM 制御の依頼のため |
| UOW-02 Session Flow | UOW-05 Settings and Context | 同期呼び出し | defaults 保存と session context 記録のため |
| UOW-03 Content Delivery | なし | - | 独立して prompt 供給契約を提供する |
| UOW-04 Audio Experience | なし | - | 独立して audio 制御契約を提供する |
| UOW-05 Settings and Context | なし | - | 独立して設定 / 文脈保持契約を提供する |

## 依存の強さと変更影響

| 依存元 | 依存先 | 強さ | 変更影響 |
| --- | --- | --- | --- |
| Experience Surface | Session Flow | 高 | start / menu action / playback state の契約変更が UI に直結する |
| Experience Surface | Settings and Context | 中 | SetupUiState や menu defaults の変更が UI に影響する |
| Session Flow | Content Delivery | 高 | prompt bundle の構造変更が進行制御に影響する |
| Session Flow | Audio Experience | 高 | cue 契約や再生順序の変更がセッション進行に影響する |
| Session Flow | Settings and Context | 中 | context metadata と defaults の構造変更が進行制御に影響する |

## 推奨開発シーケンス

### Wave 1: Foundation

- UOW-05 Settings and Context
- UOW-03 Content Delivery
- UOW-04 Audio Experience

### Wave 2: Orchestration

- UOW-02 Session Flow

### Wave 3: Experience Integration

- UOW-01 Experience Surface

## 並行開発の目安

- Track A: UOW-05 Settings and Context
- Track B: UOW-03 Content Delivery
- Track C: UOW-04 Audio Experience

上記 3 track の契約が揃った後に、UOW-02 Session Flow で統合し、最後に UOW-01 Experience Surface を接続する。

## 将来拡張時の依存候補

- FUTURE-01 を実装する段階では、Content Delivery が Settings and Context の集計結果や推薦候補契約を参照する可能性がある。
- 睡眠検知を追加する段階では、Session Flow に終了条件 provider を追加し、Audio Experience や Settings and Context との連携が増える可能性がある。

## 依存検証結果

- 循環依存は定義していない。
- 外部連携は unit 内 adapter に閉じており、独立 integration unit は不要である。
- Experience Surface を最終 wave に置くことで、2 から 3 本の並行開発方針に整合する。