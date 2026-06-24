## Purpose

三者協業モデルへの移行に伴い、PR 常時オープン運用とブランチ命名規則を追加する。

## ADDED Requirements

### Requirement: PR 常時オープン運用

執筆中の作品は、PR を常時オープンにして進捗を可視化しなければならない（SHALL）。

#### Scenario: 作品開始時の PR 作成

- **WHEN** 新しい作品プロジェクトのブランチが作成されたとき
- **THEN** draft PR をオープンしなければならない（SHALL）。PR は進捗の可視化とマルチ端末からのアクセスを目的とする

#### Scenario: 作品完成時のマージ

- **WHEN** 全ステップが完了しユーザーが最終承認を行ったとき
- **THEN** PR を main にマージしなければならない（SHALL）。中間マージは原則行わない

## MODIFIED Requirements

### Requirement: Gitブランチ戦略

システムは、各小説プロジェクトに対して独立したGitブランチを割り当て、履歴を管理しなければならない（SHALL）。

#### Scenario: ブランチ命名

- **WHEN** 新しい作品プロジェクトを開始するとき
- **THEN** `work/<project-name>` の形式でブランチを作成しなければならない（SHALL）

#### Scenario: レビュー先行コミット

- **WHEN** agy が成果物を生成したとき
- **THEN** レビューを開始する前に、その時点の状態をコミットしなければならない（MUST）
