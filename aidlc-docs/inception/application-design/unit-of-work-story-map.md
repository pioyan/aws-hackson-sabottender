# Unit of Work Story Map

## ストーリー対応マトリクス

凡例:

- P: Primary ownership
- S: Supporting ownership
- -: 関与なし

| Story | UOW-01 Experience Surface | UOW-02 Session Flow | UOW-03 Content Delivery | UOW-04 Audio Experience | UOW-05 Settings and Context |
| --- | --- | --- | --- | --- | --- |
| US-01 初回でも継続利用でも最小操作でセッションを始める | P | S | - | - | S |
| US-02 連続した情景テーマでお題を想像する | - | S | P | - | - |
| US-03 落ち着いた読み上げテンポで正解まで聴く | - | S | S | P | - |
| US-04 追加操作なしでセッションを聴き続ける | - | P | S | S | S |
| US-05 暗い画面を維持しながら必要時だけメニューを開く | P | S | - | - | - |
| US-06 再生中に BGM を選び直し音量を調整する | S | S | - | P | S |
| US-07 将来の最適化に使えるセッション文脈を保持する | - | S | S | - | P |
| FUTURE-01 眠りにつきやすかった傾向に近いセッションを提案してほしい | - | - | S | - | P |

## Unit 別の担当ストーリー

### UOW-01 Experience Surface

- Primary:
  - US-01
  - US-05
- Supporting:
  - US-06

### UOW-02 Session Flow

- Primary:
  - US-04
- Supporting:
  - US-01
  - US-02
  - US-03
  - US-05
  - US-06
  - US-07

### UOW-03 Content Delivery

- Primary:
  - US-02
- Supporting:
  - US-03
  - US-04
  - US-07
  - FUTURE-01

### UOW-04 Audio Experience

- Primary:
  - US-03
  - US-06
- Supporting:
  - US-04

### UOW-05 Settings and Context

- Primary:
  - US-07
  - FUTURE-01
- Supporting:
  - US-01
  - US-04
  - US-06

## ストーリー割当の根拠

- US-01 は開始画面の構築が主眼のため Experience Surface を primary とし、継続利用時の defaults 復元と開始処理接続のため Session Flow と Settings and Context を supporting に置く。
- US-02 は連続した情景テーマと prompt 供給の設計が中心のため Content Delivery を primary とする。
- US-03 は読み上げ順序と BGM バランスが価値の中心なので Audio Experience を primary とし、prompt 内容調整と再生起点管理のため Content Delivery と Session Flow を supporting に置く。
- US-04 は追加操作なしの連続進行が主眼なので Session Flow を primary とし、次 prompt 供給、audio 再生、context 記録が supporting となる。
- US-05 は再生中 UI とメニュー表示が中心のため Experience Surface を primary とする。
- US-06 は BGM 切り替えと音量調整が中心のため Audio Experience を primary とし、UI、反映制御、保存が supporting となる。
- US-07 と FUTURE-01 は識別情報保持と将来推薦準備が主眼のため Settings and Context を primary とする。

## トレーサビリティ検証

- すべての初期版ストーリー US-01 から US-07 には primary ownership が 1 つ割り当てられている。
- すべての初期版ストーリーには必要に応じて supporting unit が割り当てられている。
- 将来バックログ FUTURE-01 も primary / supporting の両面から割り当て済みであり、初期版の文脈保持設計と接続できる。