# Unit of Work Plan

## 目的

要件定義、ユーザーストーリー、アプリケーション設計をもとに、Android ネイティブの睡眠導入アプリを実装しやすい単位へ分解する。
この段階では、独立デプロイ可能なサービスへ分けるかどうかではなく、開発・設計・テストを進めやすい unit of work を定義する。

## 現時点の初期仮説

- 本案件は Greenfield の Android アプリであり、現時点では microservices より multi-unit monolith の方が適している。
- unit 候補は、開始導線 UI、セッション進行、コンテンツ供給、オーディオ再生、設定 / 文脈保持、外部連携 adapter のまとまりで考えるのが自然である。
- ただし、Bedrock / 音声生成サーバー連携、BGM 制御、将来パーソナライズ準備を独立 unit に切り出すかどうかは、並行開発性と依存境界の好みによって変わりうる。

## 実行チェックリスト

- [x] 分解方針と unit 粒度を確定する
- [x] ストーリー grouping 方針を確定する
- [x] 依存関係と shared resource の扱い方針を確定する
- [x] unit 一覧と責務を定義する
- [x] aidlc-docs/inception/application-design/unit-of-work.md を生成する
- [x] aidlc-docs/inception/application-design/unit-of-work-dependency.md を生成する
- [x] aidlc-docs/inception/application-design/unit-of-work-story-map.md を生成する
- [x] Greenfield のコード配置戦略を unit-of-work.md に記述する
- [x] unit 境界と依存関係を検証する
- [x] すべてのユーザーストーリーが unit に割り当て済みであることを確認する

## 記入方法

以下の [Answer]: に、選択肢の記号を記入してください。
どの選択肢にも当てはまらない場合は、最後の X を選び、その後ろに内容を追記してください。

<!-- markdownlint-disable MD053 -->

## Question 1

この案件の unit of work は、どの粒度で分けるのがよいですか。

A) Android アプリ全体を 1 unit とし、後続設計で内部モジュール化だけを管理する
B) Android アプリ内を 4 から 5 unit 程度の大きめモジュールに分ける
C) Android アプリ内を 6 unit 前後の細かめモジュールに分け、実行計画の変更単位に近づける
X) Other (please describe after [Answer]: tag below)

[Answer]: B

## Question 2

ユーザーストーリーの grouping は、何を優先して unit に束ねるのがよいですか。

A) 開始前設定、連続再生、再生中操作、将来準備といったユーザーフロー単位を優先する
B) UI、制御、コンテンツ、オーディオ、保持といった技術責務単位を優先する
C) ユーザーフローを基準にしつつ、共通の技術責務は 1 unit にまとめるハイブリッドにする
X) Other (please describe after [Answer]: tag below)

[Answer]: C

## Question 3

unit 間の依存関係と shared resource の扱いは、どの方針がよいですか。

A) SessionApplicationService のような上位オーケストレーションを中心に、共有 store や service の依存を認める
B) 同一アプリ内でも interface 境界を強め、unit 間の直接依存を最小化する
C) 実装速度を優先し、共有 state や util の利用をある程度許容する
X) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 4

この案件の開発体制を踏まえると、unit 分解はどの並行性を目安にするのがよいですか。

A) ほぼ 1 チーム / 順次開発を前提にし、並行性より理解しやすさを優先する
B) 2 から 3 本の並行開発をしやすいように、独立性の高い unit を意識する
C) アプリ本体と外部連携まわりを分け、連携境界を中心に並行開発しやすくする
X) Other (please describe after [Answer]: tag below)

[Answer]: B

## Question 5

Bedrock と音声生成サーバーのような外部連携は、どの単位で扱うのがよいですか。

A) Content unit / Audio unit の内部 adapter として持たせ、外部連携だけを独立 unit にはしない
B) 外部連携をまとめた integration unit を分け、アプリ本体から参照する
C) いまは各 unit 内 adapter としつつ、将来切り出しやすい API 契約を先に明示する
X) Other (please describe after [Answer]: tag below)

[Answer]: C

## Question 6

将来のパーソナライズ準備に関わる settings / session context は、どこへ束ねるのがよいですか。

A) 開始前設定と同じ unit に含め、設定復元と履歴識別をまとめて扱う
B) セッション進行 unit に含め、再生の文脈と一体で扱う
C) 将来拡張を見据えて、設定 / 文脈保持の専用 unit として独立させる
X) Other (please describe after [Answer]: tag below)

[Answer]: C

## Question 7

Greenfield の multi-unit monolith として進める場合、コード配置はどの形がよいですか。

A) src/{unit-name}/ と tests/{unit-name}/ を基本にし、unit ごとにまとめる
B) src/ と tests/ だけを切り、unit は文書上で管理してコード上は layer ベースで並べる
C) src/{unit-name}/ を基本にしつつ、各 unit の中で presentation / domain / data などを分ける
X) Other (please describe after [Answer]: tag below)

[Answer]: C

<!-- markdownlint-enable MD053 -->

## 次の処理

- 回答内容を読み取り、曖昧さや矛盾がないかを確認する
- 必要なら follow-up question を同じ plan に追記する
- 回答が固まったら、この plan に基づいて unit artifacts を生成する

## 回答分析結果

- unit 粒度は、Android アプリ内を 4 から 5 unit 程度の大きめモジュールに分ける方針とする。
- story grouping は、ユーザーフローを基準にしつつ、共通の技術責務は独立 unit にまとめるハイブリッド方針とする。
- unit 間の依存は、SessionApplicationService のような上位オーケストレーションを中心に管理し、共有 store / service の依存を許容する。
- 並行開発性は 2 から 3 本を目安とし、独立性の高い unit 境界を意識する。
- Bedrock と音声生成サーバーは各 unit 内 adapter としつつ、将来切り出しやすい API 契約を明示する。
- settings / session context は将来拡張を見据えた専用 unit として独立させる。
- Greenfield multi-unit monolith のコード配置は、src/{unit-name}/ を基本にしつつ、各 unit の中で presentation / domain / data などを分ける。

## 暫定的な分解イメージ

- 想定する unit 数は 5 を上限目安とする。
- 暫定候補は、開始導線体験、セッション進行、コンテンツ供給、オーディオ再生、設定 / 文脈保持である。
- 開始導線体験と設定 / 文脈保持は密接に連携するが、Q6 の回答に従い別 unit として扱う。
- コンテンツ供給 unit は Bedrock adapter を内包し、オーディオ再生 unit は音声生成サーバー adapter を内包する。

## 分析結論

- 回答内容に空欄はなく、曖昧さや矛盾は検出されなかった。
- この plan は Units Generation の生成フェーズへ進める状態にある。