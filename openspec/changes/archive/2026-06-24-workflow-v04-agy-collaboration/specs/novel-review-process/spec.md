## Purpose

三者協業モデルへの移行に伴い、レビュープロセスの主体と観点を再定義する。

## MODIFIED Requirements

### Requirement: 役割の定義（三権分立）

システムは、監督（ユーザー）とオーケストレーター（Claude Code）の二者がレビューを担当し、筆者（agy）は執筆に専念する体制を定義しなければならない（SHALL）。

#### Scenario: レビュー体制

- **WHEN** agy が成果物をコミットしたとき
- **THEN** Claude Code がまず diff を確認してレビュー所見を作成し、ユーザーに提示しなければならない（MUST）。番人ロールによる独立レビューは行わない

## ADDED Requirements

### Requirement: レビュー観点の定義

Claude Code のレビューは以下の観点を網羅しなければならない（SHALL）。

#### Scenario: 面白さの評価

- **WHEN** Claude Code が成果物をレビューするとき
- **THEN** 整合性・規約遵守に加え、物語としての面白さ・引き込まれるかについても所見を述べなければならない（SHALL）

#### Scenario: 整合性の検証

- **WHEN** Claude Code が成果物をレビューするとき
- **THEN** 上流成果物（core, assets, plot, scenes）との矛盾がないかを検証しなければならない（MUST）

#### Scenario: 規約遵守の確認

- **WHEN** Claude Code が本文（draft）をレビューするとき
- **THEN** 字下げ禁止、Show Don't Tell、過剰要約の排除、前後シーンとの接続、トーンの一貫性を確認しなければならない（MUST）
