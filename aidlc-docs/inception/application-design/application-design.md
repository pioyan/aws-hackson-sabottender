# アプリケーション設計書

## 概要

本設計は、Android ネイティブの睡眠導入アプリに対して、低刺激な再生体験を成立させるための高レベル構造を定義する。
対象は開始前設定、継続利用時の即開始、連続再生、BGM と読み上げの協調、固定 / AI 生成コンテンツ取得、自前音声生成サーバー連携、将来パーソナライズの準備までとする。

## 主要設計判断

- Android クライアント内は Presentation、Control/Application、State/Persistence、External Adapter に責務分離する。
- AI 生成コンテンツは Bedrock を使う前提で gateway を分離する。
- 音声生成はローカル Docker 配置の自前サーバーを前提にしつつ、ECS へ移行可能な gateway 抽象化を持つ。
- 開始前画面は、初回起動時は必要な設定入力を行い、継続利用時は「前回と同じで始める」を主要導線として扱い、設定変更は補助導線に留める。
- BGM と読み上げは別コンポーネントに分離し、AudioOrchestrationService が協調させる。
- 初期版の終了条件はタイマー中心とし、睡眠検知は将来拡張用の差し込みポイントとして残す。
- 将来パーソナライズ用の識別情報は、セッション制御と設定保持の責務として保存する。
- インターフェース粒度は中間粒度とし、主要ユースケース単位を保ちながら必要な補助操作も持たせる。

## 構成概要

### Presentation

- SessionSetupScreenComponent
- PlaybackScreenComponent
- OverlayMenuComponent

### Control / Application

- SessionController
- SessionTerminationPolicy
- ContentCoordinator
- SessionApplicationService
- ContentOrchestrationService
- AudioOrchestrationService
- SessionContextService
- SetupConfigurationService

### State / Persistence

- SettingsStore
- SessionContextStore

### External Adapter

- FixedContentSource
- BedrockContentGateway
- VoiceSynthesisGateway

## 設計成果物一覧

- components.md: コンポーネントの責務と境界を定義する
- component-methods.md: コンポーネントごとの主要シグネチャを定義する
- services.md: サービス層の役割とオーケストレーションを定義する
- component-dependency.md: 依存関係、通信パターン、データフローを定義する

## 実装前提

- Bedrock の認証方式や呼び出し経路の詳細は Infrastructure Design で確定する。
- 音声生成サーバーのローカル Docker 配置と ECS 移行計画は Infrastructure Design で具体化する。
- 音声・BGM の詳細な再生タイミングや失敗時挙動は Functional Design と NFR Design で詰める。

## 次段階への引き継ぎ

- Units Generation では、UI、セッション進行、コンテンツ供給、音声生成連携、BGM 制御、文脈保持を実装単位へ分割する。
- Functional Design では、初回起動 / 継続利用 / 設定変更の開始導線、セッション進行ロジック、モード差分、無音区間、フェード制御、タイマー終了の詳細ルールを定義する。
- Infrastructure Design では、Bedrock 連携、音声生成サーバー配置、将来 ECS への移行方式を具体化する。
