# Step 5: 本文 (Draft)

> **依存:** `04_scenes/` (approved)

## 構成

Scenes と同じ章・シーン構造で本文を管理します。

```
05_draft/
  chapter-01/
    scene-01.md
    scene-02.md
  chapter-02/
    scene-01.md
  ...
```

## ドラフトファイルの先頭テンプレート

```markdown
# 本文: [章]-[シーン番号] [タイトル]

> status: draft | revised | approved
> 参照: 04_scenes/chapter-XX/scene-XX.md

---

（本文）
```

## 執筆ガイドライン

- Core のトーンを常に意識する
- キャラクターの台詞・行動は `characters/<name>.md` の性格と一致させる
- Scenes で定義した「要点・ビート」をすべてカバーする
- 感情曲線の変化を描写に反映させる

## 承認チェックリスト（全体）

- [ ] 全シーンの本文が完成している
- [ ] Core のテーマが最終章まで一貫している
- [ ] キャラクターの性格・成長が一貫している
- [ ] ユーザーが通読し承認している
