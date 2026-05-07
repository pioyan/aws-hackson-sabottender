# 要件確認質問票

睡眠導入アプリの方向性を固めるため、以下の質問に回答してください。
各設問の [Answer]: の後ろに選択肢の記号を記入してください。
どの選択肢にも当てはまらない場合は、必ず最後の X を選び、その後ろに内容を追記してください。

## Question 1
このアプリを最初に提供する対象はどれですか。

A) スマートフォンWebアプリ
B) ネイティブモバイルアプリ
C) デスクトップWebアプリ
D) 複数プラットフォームを同時に狙う
X) Other (please describe after [Answer]: tag below)

[Answer]: B

## Question 2
最初のリリースで最も重視する価値はどれですか。

A) とにかく早く眠気を誘う体験
B) 遊び心のある想像体験
C) 毎晩継続して使いたくなる習慣化
D) 音と演出の心地よさ
X) Other (please describe after [Answer]: tag below)

[Answer]: XまずはAが最優先だがDも大切

## Question 3
お題の出題方式として、初期版で中心にしたいものはどれですか。

A) あらかじめ用意した固定コンテンツ
B) ルールベースで組み合わせ生成するコンテンツ
C) AIでその場生成するコンテンツ
D) 固定コンテンツと生成コンテンツの併用
X) Other (please describe after [Answer]: tag below)

[Answer]: D

## Question 4
お題ジャンルとして初期版で必須にしたいものはどれですか。

A) 動物や自然
B) 日用品や身近な物
C) 食べ物や飲み物
D) 抽象的な形や風景
X) Other (please describe after [Answer]: tag below)

[Answer]: X AとBで人間が想像しやすいものが良い

## Question 5
難易度の意味として最も近いものはどれですか。

A) ヒントの具体性が高いほど易しい
B) 音声の長さが長いほど難しい
C) 想像しやすい題材ほど易しい
D) 正解を当てにくいひねりがあるほど難しい
X) Other (please describe after [Answer]: tag below)

[Answer]: X AとC

## Question 6
正解の読み上げタイミングはどれがよいですか。

A) 特徴の読み上げが終わったらすぐ正解を伝える
B) 数秒の無音時間を置いてから正解を伝える
C) ユーザーのタップで正解を伝える
D) 設定で切り替えられるようにする
X) Other (please describe after [Answer]: tag below)

[Answer]: B

## Question 7
バックサウンドの扱いとして初期版で必要なものはどれですか。

A) 1種類だけ再生できればよい
B) 複数種類から選べればよい
C) 音量調整まで必要
D) 音量調整とタイマー停止まで必要
X) Other (please describe after [Answer]: tag below)

[Answer]: X Bと音量については段々小さくなる感じがよい

## Question 8
音声読み上げの実装方針として希望に近いものはどれですか。

A) 端末やブラウザ標準の音声機能を使いたい
B) 外部の高品質音声APIを使いたい
C) 事前収録音声を使いたい
D) まだ未定なので比較して決めたい
X) Other (please describe after [Answer]: tag below)

[Answer]: X BのAiVisSpeechを使いたい

## Question 9
基本UIについて、最初のリリースで必要なメニュー項目はどれですか。

A) ジャンル変更と難易度変更だけ
B) Aに加えてBGM選択
C) Bに加えて音量調整
D) Cに加えて音声速度や読み上げ間隔の調整
X) Other (please describe after [Answer]: tag below)

[Answer]: C

## Question 10
アプリの利用開始フローとして望ましいものはどれですか。

A) 開いたらすぐ再生開始
B) 最初にワンタップで開始
C) 最初に設定を選んでから開始
D) 初回だけチュートリアルを見せてから開始
X) Other (please describe after [Answer]: tag below)

[Answer]: X 最初にジャンルと難易度を選択した後ワンタップで開始

## Question 11
ネットワーク要件として希望に近いものはどれですか。

A) 完全オフラインで使いたい
B) 初回だけ通信し、その後はオフライン中心で使いたい
C) 常時オンラインでも問題ない
D) 未定なので実現性とコストで決めたい
X) Other (please describe after [Answer]: tag below)

[Answer]:C

## Question 12
このアプリで将来的に入れたい機能として優先度が高いものはどれですか。

A) お気に入りのお題保存
B) 睡眠ログや利用履歴
C) パーソナライズされたおすすめ
D) 毎日違うお題が届く仕組み
X) Other (please describe after [Answer]: tag below)

[Answer]: X Cのパーソナライズですが、早く眠りにつけたお題を優先したい

## Question 13
Question: Security Extensions
Should security extension rules be enforced for this project?

A) Yes — enforce all SECURITY rules as blocking constraints (recommended for production-grade applications)
B) No — skip all SECURITY rules (suitable for PoCs, prototypes, and experimental projects)
X) Other (please describe after [Answer]: tag below)

[Answer]: B

## Question 14
Question: Property-Based Testing Extension
Should property-based testing (PBT) rules be enforced for this project?

A) Yes — enforce all PBT rules as blocking constraints (recommended for projects with business logic, data transformations, serialization, or stateful components)
B) Partial — enforce PBT rules only for pure functions and serialization round-trips (suitable for projects with limited algorithmic complexity)
C) No — skip all PBT rules (suitable for simple CRUD applications, UI-only projects, or thin integration layers with no significant business logic)
X) Other (please describe after [Answer]: tag below)

[Answer]: C