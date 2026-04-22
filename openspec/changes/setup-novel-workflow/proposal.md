## Why

小説執筆のプロセスにソフトウェア開発のプラクティス（設計、実装、CI/CD、レビュー）を導入することで、AIエージェントと人間が効率的かつ一貫性を保ちながら創作活動を行えるフレームワーク「Novel Workflow (NW)」を構築します。これにより、大規模な物語でも設定矛盾を防ぎ、意図したテーマ（核）を最終稿まで維持することを目指します。

## What Changes

- 「Novel Workflow (NW) v0.1」の運用定義ドキュメントの作成
- 物語のライフサイクルを管理するための**スケルトン（雛形）ディレクトリ構造**の構築
  - `skeleton/artifacts/01_core`: テーマや問いの定義
  - `skeleton/artifacts/02_assets/`: キャラクター設定や世界観の管理
  - `skeleton/artifacts/03_plot`: 物語の構造設計
  - `skeleton/artifacts/04_scenes`: シーン単位の分解設計
  - `skeleton/artifacts/05_draft`: 本文執筆
- **スケルトンから個別の「作業用プロジェクト」を生成する仕組み**の定義
- 成果物間の整合性を検証するための基本的な枠組みの定義

## Capabilities

### New Capabilities
- `novel-structure-framework`: 小説の各フェーズを成果物として管理し、上流工程を下流工程の制約（Spec）として扱う仕組み。
- `novel-project-management`: スケルトンから個別の作業用ディレクトリ（`projects/<name>/`）を生成・管理する機能。
- `novel-review-process`: 生成された本文やプロットが、上流で定義された制約を遵守しているかを確認するプロセス。

### Modified Capabilities
- なし

## Impact

- `novel/` ディレクトリ配下に新しいドキュメント構造が作成されます。
- 現時点での既存コードベースへの直接的な影響はありませんが、将来的に CI パイプラインとの統合が検討されます。
