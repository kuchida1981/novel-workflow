## Context

Novel Workflow v0.3 は「三権分立」で監督・筆者・番人の役割を定義したが、筆者と番人は同一セッション内のペルソナ切り替えに留まり、実質的には Claude Code が全てを担当していた。一方、video-ratings プロジェクトでは Claude Code → agy (Antigravity CLI) の MCP ブリッジ連携が安定稼働しており、「設計は Claude Code、実装は agy」という分業モデルが確立されている。

本変更はこのパターンを小説執筆に移植し、三者が物理的に異なるシステムとして協業する体制を構築する。

## Goals / Non-Goals

**Goals:**

- ユーザー・Claude Code・agy の三者協業モデルを CLAUDE.md とワークフローに定義する
- 全5ステップで共通の「設計 → 執筆 → レビュー」ループを確立する
- agy MCP 連携の呼び出し規約・プロンプト構成・リカバリルールを策定する
- PR 常時オープン運用を定義する
- レビュー観点に面白さの評価を含める

**Non-Goals:**

- 5ステップ（core/assets/plot/scenes/draft）の構成変更
- 既存作品（artificial-sincerity 等）への遡及適用
- agy 以外の外部ツール連携
- 組版・出版ワークフローの設計

## Decisions

### 1. 三者の役割分担

| 役割 | 担当 | 責任 |
|------|------|------|
| 監督 | ユーザー | アイデア出し、最終レビュー、承認ゲート |
| オーケストレーター | Claude Code | 素案の提案、agy への指示準備、レビュー（面白さ含む）、コンテキスト管理 |
| 筆者 | agy | 成果物の執筆（全5ステップ）、git commit |

**代替案**: agy をレビュー（番人）にも使う（video-ratings の Step 2.5 パターン）。小説執筆では客観的な検証指標が少なく、agy レビューの付加価値が限定的と判断し不採用。

### 2. 統一ループ

全ステップで以下のサイクルを回す:

```
Phase 1: 設計 (User × Claude Code)
  ユーザーのアイデアを元に Claude Code が素案を整理
  合意形成 → agy への指示内容を確定

Phase 2: 執筆 (agy via MCP)
  入力: 上流成果物 + Phase 1 の合意事項 + 執筆規約
  出力: 成果物ファイル + git commit

Phase 3: レビュー (User × Claude Code)
  Claude Code がまず diff をレビューし所見を提示
  ユーザーが承認 or リテイク
  リテイク時: Phase 1 or 2 に戻る
```

### 3. agy 呼び出し方式

MCP ブリッジを推奨、Bash をフォールバックとする（video-ratings と同様）。

- 新規タスク: `mcp__agy__antigravity_ask`
- 追加指示・継続: `mcp__agy__antigravity_continue`
- 状態確認: `mcp__agy__antigravity_status`
- 並列生成（assets 等）: `mcp__agy__antigravity_swarm`（ユーザーと相談の上）

### 4. ステップ別の粒度とコンテキスト

| Step | agy 1回の単位 | 入力コンテキスト | agy の裁量 |
|------|--------------|-----------------|-----------|
| core | core.md 全体 | ユーザーのアイデア + 素案 | 低（忠実に構造化） |
| assets | ファイル単位 (world / 各キャラ) | core + 素案 | 中 |
| plot | plot.md 全体 or 章グループ | core + assets + 素案 | 中 |
| scenes | 章単位 or シーン単位（Claude Code が判断） | core + assets + plot + 素案 | 中〜高 |
| draft | 章節単位（1章の1-2シーン程度） | 全上流 + scene仕様 + 直前draft | 高（創造的自由） |

粒度の最終判断は Claude Code が作品の規模・メッセージ性を考慮して行う。

### 5. agy への指示内容

テンプレート化はしない。Claude Code がステップと状況に応じて以下から構成する:

- 上流の成果物（該当部分の引用 or 全文）
- ユーザーと Claude Code の合意事項（注意点、方向性、避けたいこと）
- 執筆規約（字下げ禁止、Show Don't Tell 等）
- 直前の成果物の末尾（接続のため、draft フェーズ）
- コミット指示とハング予防ルール

**コンテキストの削ぎ落とし**: 下流ステップでは入力量が膨大になるため、Claude Code が関連性を判断して取捨選択する。

### 6. レビュー観点

| 観点 | 担当 |
|------|------|
| 面白さ・引き込まれるか | ユーザー + Claude Code |
| 上流成果物との整合性・矛盾 | ユーザー + Claude Code |
| 規約遵守（字下げ、Show Don't Tell 等） | Claude Code |
| 前後シーンとの接続 | Claude Code |
| トーンの一貫性 | Claude Code |

Claude Code は面白さの判断にも積極的に関与し、ユーザーへの気づきを提供する。

### 7. PR・ブランチ運用

- ブランチ名: `work/<project-name>`
- PR: 作品開始時にオープン、作品完成時にマージ
- 目的: 進捗の可視化、マルチ端末からのアクセス
- 中間マージは原則なし（必要に応じてユーザー判断）

### 8. OpenSpec との棲み分け

- **OpenSpec**: ワークフロー・フレームワーク自体の変更、大規模バックトラック
- **5ステップ + 三者協業**: 通常の執筆プロジェクトの進行

## Risks / Trade-offs

- **[agy の出力品質]** → 品質はイテレーション（レビュー → リテイク）で上げる方針。初回の完璧さは求めない
- **[コンテキスト膨張]** → Claude Code がオーケストレーターとして取捨選択。下流ステップほど注意が必要
- **[agy MCP の安定性]** → Bash フォールバックを用意。2回連続失敗時は Claude Code が直接執筆に切り替え
- **[創造性 vs 制御]** → agy に裁量を与え、結果に対してレビューで調整する。過度な指示で創造性を殺さない
