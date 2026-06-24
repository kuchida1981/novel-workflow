## Why

現行の Novel Workflow v0.3 では「三権分立」（監督・筆者・番人）を定義しているが、筆者と番人は同一 AI セッション内での役割切り替えに過ぎない。video-ratings プロジェクトで Claude Code と Antigravity CLI (agy) の MCP ブリッジによる協業パターンが確立されたため、これを小説執筆フローに導入し、物理的に異なるシステムによる三者協業を実現する。

## What Changes

- **三者協業モデルの導入**: ユーザー（監督）、Claude Code（オーケストレーター/レビュアー）、agy（筆者）の三者を明確に分離
- **統一ループの確立**: 全5ステップ（core → assets → plot → scenes → draft）で共通の「設計 → 執筆 → レビュー」サイクルを適用
- **agy MCP 連携の定義**: `mcp__agy__antigravity_ask` / `antigravity_continue` / `antigravity_status` を使った呼び出し規約、プロンプト構成、失敗時リカバリを策定
- **PR 常時オープン運用**: 1作品=1ブランチ、PR を進捗可視化とマルチ端末アクセスの手段として常時オープンにする
- **レビュー体制の刷新**: 番人ロールを廃止し、ユーザーと Claude Code が面白さ・整合性・規約遵守を含むレビューを担当
- **CLAUDE.md の新設**: video-ratings の CLAUDE.md をベースに、小説執筆向けのワークフロー・agy 連携ルール・レビュー規約を定義
- **OpenSpec の棲み分け**: OpenSpec はフレームワーク変更・大規模バックトラックに使い、通常の執筆進行には5ステップ+三者協業ループを使う

## Capabilities

### New Capabilities
- `agy-writing-integration`: agy MCP ブリッジによる執筆連携（呼び出し規約、プロンプト構成、コンテキスト管理、失敗時リカバリ）
- `three-party-collaboration`: 三者協業モデルと統一ループの定義（役割分担、設計→執筆→レビューサイクル、ステップ別粒度）

### Modified Capabilities
- `novel-review-process`: 番人ロールの廃止、レビュー主体を User + Claude Code に変更、面白さの評価をレビュー観点に追加
- `novel-project-management`: PR 常時オープン運用の追加、ブランチ命名規則の明確化（マージ=作品完成）

## Impact

- `skeleton/docs/workflow.md`: v0.4 として三者協業モデルと統一ループを反映
- `CLAUDE.md`（新規）: プロジェクトルートに agy 連携ルール・ワークフロー・レビュー規約を定義
- `openspec/specs/novel-review-process/spec.md`: 三権分立 → 三者協業への移行を反映
- `openspec/specs/novel-project-management/spec.md`: PR 運用の追加
- 既存の作品プロジェクト（`projects/active/` 配下）には直接の影響なし（新ワークフローは次の作品から適用）
