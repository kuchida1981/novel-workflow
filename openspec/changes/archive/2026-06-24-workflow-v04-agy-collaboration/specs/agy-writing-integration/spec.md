## Purpose

agy (Antigravity CLI) を MCP ブリッジ経由で小説執筆に活用するための連携規約を定義する。呼び出し方式、プロンプト構成、コンテキスト管理、失敗時リカバリを含む。

## ADDED Requirements

### Requirement: MCP ブリッジによる agy 呼び出し

Claude Code は MCP ブリッジを優先して agy と通信しなければならない（SHALL）。MCP が利用不可能な場合に限り Bash フォールバックを使用する。

#### Scenario: MCP 方式での執筆依頼

- **WHEN** Claude Code が agy に執筆タスクを依頼するとき
- **THEN** `mcp__agy__antigravity_ask` を使用してプロンプトを送信しなければならない（MUST）

#### Scenario: MCP 利用不可時のフォールバック

- **WHEN** セッション開始時に `mcp__agy__antigravity_status` が応答しないとき
- **THEN** Claude Code は Bash 方式（`agy --dangerously-skip-permissions --print`）にフォールバックしなければならない（SHALL）

### Requirement: agy へのプロンプト構成

Claude Code は agy に対し、ステップと状況に応じた適切なコンテキストを含むプロンプトを構成しなければならない（SHALL）。

#### Scenario: 上流成果物の提供

- **WHEN** agy に執筆を依頼するとき
- **THEN** プロンプトには上流の成果物（対象ステップより前のステップの成果物）を含めなければならない（MUST）

#### Scenario: 合意事項の伝達

- **WHEN** ユーザーと Claude Code が Phase 1（設計）で合意に至ったとき
- **THEN** 合意事項（注意点、方向性、避けたいこと）を agy へのプロンプトに含めなければならない（MUST）

#### Scenario: 執筆規約の付与

- **WHEN** agy に本文（draft）の執筆を依頼するとき
- **THEN** 字下げ禁止、Show Don't Tell 等の執筆規約をプロンプトに含めなければならない（MUST）

#### Scenario: コンテキスト量の管理

- **WHEN** 下流ステップで入力コンテキストが膨大になるとき
- **THEN** Claude Code は作品の文脈に基づき関連性の低い部分を省略し、トークン効率を管理しなければならない（SHALL）

### Requirement: agy 出力後のコミット

agy は執筆完了後、対象ファイルのみを git commit しなければならない（SHALL）。

#### Scenario: 成果物のコミット

- **WHEN** agy が成果物の執筆を完了したとき
- **THEN** `git add <対象ファイル> && git commit` を実行し、`git add -A` や `git add .` は使用してはならない（MUST）

### Requirement: 失敗時リカバリ

agy がタイムアウト・エラーで失敗した場合、Claude Code は段階的にリカバリしなければならない（SHALL）。

#### Scenario: 途中成果があるケース

- **WHEN** agy が失敗し、`git status` で途中成果が確認できるとき
- **THEN** Claude Code は `mcp__agy__antigravity_continue` で残り作業を指示しなければならない（SHALL）

#### Scenario: 2回連続の失敗

- **WHEN** agy が同一タスクで2回連続して失敗したとき
- **THEN** Claude Code が直接執筆に切り替えなければならない（SHALL）

### Requirement: ハング予防ルール

agy へのすべてのプロンプトに、対話的入力の禁止ルールを含めなければならない（SHALL）。

#### Scenario: 対話的入力の回避

- **WHEN** agy にプロンプトを送信するとき
- **THEN** 「対話的入力を求めるコマンドは実行しないこと」「git push は行わないこと」「`git add -A` は禁止」のルールを含めなければならない（MUST）
