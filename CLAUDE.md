# CLAUDE.md — novel-workflow プロジェクト

## AI ツール役割分担（三者協業モデル）

このプロジェクトではユーザー・Claude Code・Antigravity CLI (agy) の三者が協業する。

| 役割 | 担当 | 責任 |
|------|------|------|
| **監督** | **ユーザー** | アイデア出し、最終レビュー、承認ゲート |
| **オーケストレーター** | **Claude Code** | 素案の提案、agy への指示準備、レビュー（面白さ含む）、コンテキスト管理 |
| **筆者** | **agy** | 成果物の執筆（全5ステップ）、git commit |

---

## 統一ループ（設計 → 執筆 → レビュー）

全5ステップ（core, assets, plot, scenes, draft）で以下の共通サイクルを回す。

```
Phase 1: 設計 (User × Claude Code)
  ユーザーのアイデアを元に Claude Code が素案を整理
  → 対話で合意形成 → agy への指示内容を確定

Phase 2: 執筆 (agy via MCP)
  入力: 上流成果物 + Phase 1 の合意事項 + 執筆規約
  → agy が成果物を生成 → git commit

Phase 3: レビュー (User × Claude Code)
  Claude Code がまず diff をレビューし所見を提示
  → ユーザーが承認 or リテイク
  → リテイク時: Phase 1 or 2 に戻る
```

---

## Claude Code から agy を呼び出す方法

### 方式の選択

| 方式 | 条件 | 利点 |
|------|------|------|
| **MCP（推奨）** | `mcp__agy__antigravity_status` が利用可能 | 会話継続が自然、タイムアウト問題が少ない |
| **Bash（フォールバック）** | MCP 未セットアップの環境 | セットアップ不要、どの環境でも動く |

セッション開始時に `mcp__agy__antigravity_status` が呼べるか確認し、利用可能なら MCP 方式を使う。

### MCP 方式（推奨）

- 新規タスク: `mcp__agy__antigravity_ask`
- 追加指示・継続: `mcp__agy__antigravity_continue`
- 状態確認: `mcp__agy__antigravity_status`
- 並列生成（assets 等、ユーザーと相談の上）: `mcp__agy__antigravity_swarm`

### Bash 方式（フォールバック）

```bash
GIT_TERMINAL_PROMPT=0 CI=true \
  agy --dangerously-skip-permissions --print-timeout 3m --print "<プロンプト>" 2>&1
```

| タスク規模 | タイムアウト |
|-----------|------------|
| 小（1ファイル） | `--print-timeout 3m` |
| 中（2-3ファイル） | `--print-timeout 5m` |
| 大（4ファイル以上） | `--print-timeout 8m`（分割を検討） |

### agy へのプロンプト構成

テンプレートは固定しない。Claude Code がステップと状況に応じて以下から構成する:

- 上流の成果物（該当部分の引用 or 全文）
- ユーザーと Claude Code の合意事項（注意点、方向性、避けたいこと）
- 執筆規約（字下げ禁止、Show Don't Tell 等）
- 直前の成果物の末尾（接続のため、draft フェーズ）
- コミット指示とハング予防ルール

### agy プロンプトの必須ルール

agy へのすべてのプロンプトに、以下のルールを必ず含める:

```
制約:
- 対話的入力（y/n, パスワード等）を求めるコマンドは絶対に実行しないこと
- 必ず非対話フラグ（--yes, -y, --no-input 等）を付けること
- git push, npm publish など外部サービスへの送信は行わないこと
- 対話的入力が必要な状況に遭遇したら、実行せず [QUESTION] で報告すること
- git add は指定ファイルのみ。`git add -A` や `git add .` は禁止
```

### agy との対話ループ

agy の出力に `[QUESTION]` が含まれる場合:

```
agy 実行 (1回目)
  ↓ 出力を解析
  ├─ [QUESTION] なし → 執筆完了、レビューへ
  └─ [QUESTION] あり → 質問ごとに判定:
       ├─ Claude Code が回答できる → 回答をまとめる
       └─ 判断つかない / センシティブ → ユーザーに確認
       ↓
     agy 実行 (2回目: 回答を追加コンテキストとして渡す)
```

- MCP 方式: `antigravity_continue` で追加コンテキストを渡す
- Bash 方式: 新しい `agy --print` 呼び出しに回答を含める
- ループ上限は 3 回。3 回で解決しない場合は Claude Code が直接執筆に切り替える

### agy 失敗時のリカバリ

```
失敗発生
  ↓
git status / git diff で途中成果を確認
  ├─ 成果あり → 継続指示を送る（antigravity_continue）
  └─ 成果なし → 新規セッションで別アプローチを試行
      ↓
再試行も失敗（2回連続）
  ↓
Claude Code が直接執筆に切り替える
```

---

## ステップ別の粒度・コンテキスト管理

| Step | agy 1回の単位 | 入力コンテキスト | agy の裁量 |
|------|--------------|-----------------|-----------|
| core | core.md 全体 | ユーザーのアイデア + 素案 | 低（忠実に構造化） |
| assets | ファイル単位 (world / 各キャラ) | core + 素案 | 中 |
| plot | plot.md 全体 or 章グループ | core + assets + 素案 | 中 |
| scenes | 章単位 or シーン単位（Claude Code が判断） | core + assets + plot + 素案 | 中〜高 |
| draft | 章節単位（1章の1-2シーン程度） | 全上流 + scene仕様 + 直前draft | 高（創造的自由） |

粒度の最終判断は Claude Code が作品の規模・メッセージ性を考慮して行う。

**コンテキストの削ぎ落とし:** 下流ステップでは入力量が膨大になるため、Claude Code が関連性を判断して取捨選択する。全てを渡すのではなく、当該シーンに関連する部分を中心に構成する。

**並列処理:** assets など独立した要素は `antigravity_swarm` で並列生成できる（ユーザーと相談の上）。draft は手戻りリスクがあるため原則直列で進める。

---

## レビュー観点

Claude Code のレビューは以下の観点を網羅する。

| 観点 | 担当 |
|------|------|
| 面白さ・引き込まれるか | ユーザー + Claude Code |
| 上流成果物との整合性・矛盾 | ユーザー + Claude Code |
| 規約遵守（字下げ、Show Don't Tell 等） | Claude Code |
| 前後シーンとの接続 | Claude Code |
| トーンの一貫性 | Claude Code |

Claude Code は面白さの判断にも積極的に関与し、ユーザーへの気づきを提供する。

---

## Git・ブランチ・PR 運用

### ブランチ命名

```
work/<project-name>    # 作品ごとのトピックブランチ
feature/<change-name>  # フレームワーク変更（OpenSpec change）
```

### PR 常時オープン

- 作品の執筆を開始したら draft PR をオープンする
- 目的: 進捗の可視化、マルチ端末からのアクセス
- マージ = 作品の完成（中間マージは原則なし）

### コミットルール

- agy が成果物をコミットする（`git add <対象ファイル>` のみ、`git add -A` 禁止）
- `--no-verify` の使用は禁止。フックが失敗したら根本原因を修正する

---

## OpenSpec との棲み分け

| 用途 | 使うもの |
|------|---------|
| ワークフロー・フレームワーク自体の変更 | OpenSpec（`/opsx:propose` → `/opsx:apply`） |
| 大規模バックトラック（plot の根本改修等） | OpenSpec |
| 通常の執筆プロジェクトの進行 | 5ステップ + 三者協業ループ |

### OpenSpec チートシート

| スキル | 用途 |
|--------|------|
| `/opsx:explore` | アイデア・問題を探索する（実装しない） |
| `/opsx:propose` | change proposal を一括生成 |
| `/opsx:apply` | tasks.md のタスクを順に実装 |
| `/opsx:sync` | delta spec を main spec にマージ |
| `/opsx:archive` | 完了した change をアーカイブ |

---

## 執筆・フォーマット規約

### Markdownソースの書式
- **字下げ禁止:** 段落行頭に全角スペースを挿入しないこと
- **改行:** 原則として、一段落ごとに1行の空行を入れる

### 表現技法
- **Show, Don't Tell:** 感情や状況を地の文で「説明」するのではなく、キャラクターの行動や五感を通じて読者に伝える
- **過剰要約の排除:** シーンが既に描写している内容を、語り手が再度要約して説明することを避ける
- **リサーチの先行:** 専門的な小道具が登場する場合、執筆前にリアリティの精査を行う
