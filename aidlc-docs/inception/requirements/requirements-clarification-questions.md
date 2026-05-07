# 要件追加確認質問票

要件定義書を確定する前に、実装方針に直結する曖昧さを3点だけ確認させてください。
各設問の [Answer]: の後ろに選択肢の記号を記入してください。
どの選択肢にも当てはまらない場合は、必ず最後の X を選び、その後ろに内容を追記してください。

## Ambiguity 1: 初期リリースの提供範囲
「ネイティブモバイルアプリ」という方向性は明確ですが、初期リリースでどこまで対象にするかが未確定です。

### Clarification Question 1
初期リリースで対象にしたいOSはどれですか。

A) iOSのみ
B) Androidのみ
C) iOSとAndroidの両方
D) まずは最短で出せる構成を優先し、OSは実装提案を見て決めたい
X) Other (please describe after [Answer]: tag below)

[Answer]: B

## Ambiguity 2: 生成コンテンツの方式
「固定コンテンツと生成コンテンツの併用」は明確ですが、初期版で採用する生成方法が未確定です。

### Clarification Question 2
初期版で入れたい生成コンテンツの方式はどれですか。

A) 固定コンテンツに加えて、ルールベース生成のみ
B) 固定コンテンツに加えて、AI生成のみ
C) 固定コンテンツに加えて、ルールベース生成とAI生成の両方
D) 初期版は固定コンテンツ中心にして、生成は将来機能に回したい
X) Other (please describe after [Answer]: tag below)

[Answer]: B

## Ambiguity 3: 1セッションの進み方
睡眠導入アプリとして、1問ごとに止まるのか、自動で流れ続けるのかで体験設計が変わります。

### Clarification Question 3
初期版の再生フローとして望ましいものはどれですか。

A) 1問ごとに停止し、次はユーザー操作で進む
B) 正解の読み上げ後、自動で次のお題へ進む
C) 指定回数だけ自動で連続再生する
D) タイマーや睡眠モードが終わるまで自動で連続再生する
X) Other (please describe after [Answer]: tag below)

[Answer]: D