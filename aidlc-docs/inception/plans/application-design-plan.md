# Application Design Plan

## 目的

要件定義書とユーザーストーリーをもとに、実装前にアプリケーションの主要コンポーネント、責務境界、サービス層、依存関係を定める。
この段階では高レベル設計に集中し、詳細な業務ロジックやアルゴリズムは後続の Functional Design で扱う。

## 対象範囲

- Android クライアントの画面層と再生制御
- セッション進行とモード切替のアプリ内責務
- 固定コンテンツと AI 生成コンテンツの供給境界
- 自前音声生成サーバーとの連携境界
- BGM 制御とオーディオ再生責務
- 将来のパーソナライズ準備に必要な識別情報の保持責務

## 設計方針の初期仮説

- アプリ内では UI、セッション制御、音声再生、コンテンツ取得、設定保持を分離した方が責務が明確になる。
- 自前音声生成サーバーと AI 生成基盤は、Android クライアントから見ると少数のアプリケーションサービス経由で扱う方が依存が整理しやすい。
- 睡眠検知は UI ではなく、セッション終了条件を管理する制御コンポーネント側で扱う方が一貫性を保ちやすい。

## 実行チェックリスト

- [x] コンポーネント候補を洗い出す
- [x] コンポーネントごとの責務境界を定義する
- [x] コンポーネントメソッドの粒度とインターフェース方針を定義する
- [x] サービス層の責務とオーケストレーション方針を定義する
- [x] コンポーネント依存関係と通信パターンを定義する
- [x] components.md を作成する
- [x] component-methods.md を作成する
- [x] services.md を作成する
- [x] component-dependency.md を作成する
- [x] application-design.md を作成する
- [x] 設計成果物の整合性を確認する

## 生成予定成果物

- [x] aidlc-docs/inception/application-design/components.md
- [x] aidlc-docs/inception/application-design/component-methods.md
- [x] aidlc-docs/inception/application-design/services.md
- [x] aidlc-docs/inception/application-design/component-dependency.md
- [x] aidlc-docs/inception/application-design/application-design.md

<!-- markdownlint-disable MD053 -->

## 設計確認質問

以下の [Answer]: に、選択肢の記号を記入してください。
どの選択肢にも当てはまらない場合は、最後の X を選び、その後ろに内容を追記してください。

## Question 1

Android アプリ内のコンポーネント分割として、どの粒度がよいですか。

A) 画面、セッション制御、オーディオ制御、コンテンツ取得、設定保持を分ける
B) 画面と制御をまとめ、オーディオ制御とコンテンツ取得だけ分ける
C) できるだけ少数コンポーネントにまとめる
X) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 2

AI 生成コンテンツ取得と音声生成サーバー連携は、アプリから見てどう分けるのがよいですか。

A) 別々のサービスとして分ける
B) 1つのバックエンド連携サービスとしてまとめる
C) 初期版は音声生成だけ外部連携し、AI 生成はアプリ内で抽象化する
X) Other (please describe after [Answer]: tag below)

[Answer]: X AI 生成コンテンツ取得はAWSのBedrockを使ってほしい。音声生成サーバー連携はローカルで動かすが、AWSへの移行の可能性あり（Dockerで動作させ、AWS ECSへの移行を検討）

## Question 3

睡眠検知の責務は、どこに置くのがよいですか。

A) セッション制御コンポーネントが終了条件の一部として管理する
B) 専用の睡眠検知コンポーネントを分け、セッション制御から利用する
C) 初期版では睡眠検知を簡易化し、タイマー中心の設計に寄せる
X) Other (please describe after [Answer]: tag below)

[Answer]: X 後で決める

## Question 4

再生中の BGM と読み上げ音声の制御は、どう分けるのがよいですか。

A) BGM と読み上げを別コンポーネントに分け、上位のオーディオサービスで協調させる
B) 単一のオーディオ制御コンポーネントでまとめて扱う
C) UI 側から個別に制御する前提でよい
X) Other (please describe after [Answer]: tag below)

[Answer]: C

## Question 5

将来のパーソナライズ準備として保持する識別情報は、どの層で扱うのがよいですか。

A) セッション制御と設定保持の責務としてアプリ内で管理する
B) コンテンツ取得層で管理する
C) 将来対応なので今回は設計上だけ触れ、コンポーネント責務には入れない
X) Other (please describe after [Answer]: tag below)

[Answer]: C

## Question 6

Application Design でのインターフェース粒度はどれがよいですか。

A) 高レベルのユースケース単位でメソッドを定義する
B) データ取得・再生・状態更新など細かい操作単位で定義する
C) A と B の中間で、主要ユースケースごとに必要な補助操作も持たせる
X) Other (please describe after [Answer]: tag below)

[Answer]: X 比較検討表を提示してください

## 承認

この計画で Application Design を進めてよい場合は、以下に回答してください。

A) この計画で進めてよい
B) 計画を修正してから進めたい
X) Other (please describe after [Answer]: tag below)

[Answer]: A

## 追加確認質問

現時点の回答には、実装時の責務境界へ直接影響する曖昧さがいくつかあります。
以下の質問に回答すると、Application Design の成果物を矛盾なく作成できます。

## Question 7

睡眠検知は「後で決める」とのことですが、Application Design ではどの形で枠だけ先に定義しますか。

A) 専用の睡眠検知コンポーネントまたはインターフェースを用意し、実装方式は後で決める
B) 今回はタイマー終了だけを確実な終了条件として設計し、睡眠検知は将来拡張として扱う
C) セッション制御コンポーネントの内部責務としてまとめ、詳細は後で詰める
X) Other (please describe after [Answer]: tag below)

[Answer]: B

## Question 8

Question 1 ではオーディオ制御コンポーネントを分ける方向、Question 4 では UI 側から個別制御する方向になっており、責務が競合しています。
最終的にどちらを採用しますか。

A) UI は操作イベントだけを渡し、単一のオーディオ制御コンポーネントが BGM と読み上げをまとめて扱う
B) UI は操作イベントだけを渡し、BGM と読み上げは別コンポーネントに分けて上位サービスで協調させる
C) UI が BGM と読み上げを直接個別制御する
X) Other (please describe after [Answer]: tag below)

[Answer]: B

## Question 9

FR-7 と US-07 では、初期版からコンテンツ種別や設定条件を識別できる構成が必要です。
Question 5 の回答では「今回は責務に入れない」となっているため、ここを確定してください。

A) 要件どおり、セッション制御と設定保持の責務として扱う
B) 要件どおり、コンテンツ取得層の責務として扱う
C) 今回は責務に入れず、後で requirements.md と stories.md も修正する
X) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 10

Question 6 で比較検討表の提示要望があったため、以下に整理します。

| 選択肢 | 粒度 | 長所 | 注意点 | この案件への適性 |
| --- | --- | --- | --- | --- |
| A | 高レベルのユースケース単位 | 画面や利用シナリオに対応しやすい | 内部処理の分割が見えにくくなる | 中 |
| B | 細かい操作単位 | 再利用しやすく実装粒度に近い | Application Design としては細かすぎて責務が散りやすい | 低 |
| C | 中間粒度 | ユースケースを保ちつつ補助操作も表現できる | 境界を揃える設計判断が必要 | 高 |

この比較を踏まえて、Application Design のインターフェース粒度はどれにしますか。

A) 高レベルのユースケース単位
B) 細かい操作単位
C) 中間粒度
X) Other (please describe after [Answer]: tag below)

[Answer]: C

<!-- markdownlint-enable MD053 -->

## 確定した設計判断

- Android クライアントは、画面、セッション制御、オーディオ再生、コンテンツ取得、設定保持を分離する。
- AI 生成コンテンツ取得は Bedrock を利用する adapter として扱う。
- 音声生成サーバーはローカル Docker 稼働を前提にしつつ、将来 ECS へ移行できる gateway 抽象化を持つ。
- 初期版の終了条件はタイマー中心で設計し、睡眠検知は将来拡張に回す。
- BGM と読み上げは別コンポーネントに分け、上位サービスで協調させる。
- 将来パーソナライズ用の識別情報は、セッション制御と設定保持の責務として扱う。
- Application Design のメソッド粒度は中間粒度を採用する。
