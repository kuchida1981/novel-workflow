## ADDED Requirements

### Requirement: ディレクトリ構造の自動生成
システムは、小説プロジェクトのための標準的なディレクトリ構造（スケルトン）を提供しなければならない（SHALL）。

#### Scenario: 構造の生成に成功する
- **WHEN** セットアップコマンドが実行されたとき
- **THEN** `skeleton/artifacts/01_core`、`skeleton/artifacts/02_assets/characters`、`skeleton/artifacts/03_plot`、`skeleton/artifacts/04_scenes`、`skeleton/artifacts/05_draft` の各ディレクトリが作成される

### Requirement: アーティファクトのライフサイクル管理
フレームワークは、上流のアーティファクトが下流のアーティファクトの要件として機能する、線形な依存関係チェーンを管理しなければならない（SHALL）。

#### Scenario: 上流のコンテキストへのアクセス
- **WHEN** ステップ5でシーンの初稿を生成するとき
- **THEN** システムは `core.md`、`assets/`、`plot.md` からコンテキストを読み込み、組み込むことができなければならない（MUST）

### Requirement: ワークフロー・ドキュメント
システムは、プロセスを説明する包括的なワークフロー・ドキュメント（`novel/docs/workflow.md`）を含まなければならない（SHALL）。

#### Scenario: ワークフロー・ガイドが利用可能
- **WHEN** ユーザーがフレームワークを初期化したとき
- **THEN** ステップ、完了条件、バックトラック・ルールが記載された `workflow.md` ファイルが作成される
