## Purpose

ユーザー・Claude Code・agy の三者協業モデルと、全5ステップで共通の「設計 → 執筆 → レビュー」統一ループを定義する。

## ADDED Requirements

### Requirement: 三者の役割定義

システムはユーザー（監督）、Claude Code（オーケストレーター）、agy（筆者）の三者を明確に分離し、それぞれに責任を割り当てなければならない（SHALL）。

#### Scenario: ユーザーの役割

- **WHEN** 執筆プロジェクトが進行中のとき
- **THEN** ユーザーはアイデア出しと最終レビュー（承認/リテイク判定）を担当しなければならない（SHALL）

#### Scenario: Claude Code の役割

- **WHEN** 執筆プロジェクトが進行中のとき
- **THEN** Claude Code は素案の提案、agy への指示準備、レビュー、コンテキスト管理を担当しなければならない（SHALL）

#### Scenario: agy の役割

- **WHEN** Phase 2（執筆）に入ったとき
- **THEN** agy は指示に基づいて成果物を執筆し git commit しなければならない（SHALL）

### Requirement: 統一ループ（設計 → 執筆 → レビュー）

全5ステップ（core, assets, plot, scenes, draft）は共通の3フェーズサイクルで進行しなければならない（SHALL）。

#### Scenario: Phase 1（設計）

- **WHEN** あるステップの作業を開始するとき
- **THEN** ユーザーと Claude Code が対話し、アイデアの整理と方向性の合意を行い、agy への指示内容を確定しなければならない（SHALL）

#### Scenario: Phase 2（執筆）

- **WHEN** Phase 1 で合意に至ったとき
- **THEN** Claude Code が agy に上流成果物と合意事項を含むプロンプトを送信し、agy が成果物を生成・コミットしなければならない（SHALL）

#### Scenario: Phase 3（レビュー）

- **WHEN** agy が成果物をコミットしたとき
- **THEN** Claude Code がまず diff をレビューして所見をユーザーに提示し、ユーザーが承認またはリテイクを判定しなければならない（SHALL）

#### Scenario: リテイクの処理

- **WHEN** ユーザーがリテイクを判定したとき
- **THEN** Phase 1（方向性の見直し）または Phase 2（修正指示で再執筆）に戻らなければならない（SHALL）

### Requirement: ステップ別粒度の決定

Claude Code は作品の規模・メッセージ性を考慮し、各ステップにおける agy 1回の作業単位を決定しなければならない（SHALL）。

#### Scenario: draft フェーズの粒度

- **WHEN** draft フェーズで agy に執筆を依頼するとき
- **THEN** 章節単位（1章の1-2シーン程度）を基本単位としなければならない（SHALL）

#### Scenario: assets の並列処理

- **WHEN** assets フェーズで独立した要素（world と各キャラクター）を設計するとき
- **THEN** Claude Code はユーザーと相談の上、swarm による並列生成を選択できる（MAY）

### Requirement: agy への創造的裁量

agy には執筆における創造的裁量を与え、品質はレビューとリテイクの繰り返しで向上させなければならない（SHALL）。

#### Scenario: 創造性の許容

- **WHEN** agy に執筆を依頼するとき
- **THEN** 過度に具体的な指示で agy の創造性を制約してはならない。方向性と制約を与え、表現の詳細は agy の裁量に委ねなければならない（SHALL）
