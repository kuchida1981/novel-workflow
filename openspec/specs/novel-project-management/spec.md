## ADDED Requirements

### Requirement: プロジェクトの初期化
システムは、指定されたスケルトンから新しい作業用ディレクトリを作成する機能を提供しなければならない（SHALL）。

#### Scenario: プロジェクトディレクトリの作成
- **WHEN** プロジェクト名「my-novel」で初期化が実行されたとき
- **THEN** `projects/my-novel/` ディレクトリが作成され、`skeleton/` 配下のすべてのディレクトリ構造とファイルがコピーされる

### Requirement: プロジェクト固有のワークフロー管理
各作業ディレクトリは、独立したライフサイクルとステータスを持たなければならない（SHALL）。

#### Scenario: 独立した進捗管理
- **WHEN** `projects/novel-a/` でステップが進んだとき
- **THEN** その進捗は `projects/novel-b/` や `skeleton/` に影響を与えてはならない（MUST）
