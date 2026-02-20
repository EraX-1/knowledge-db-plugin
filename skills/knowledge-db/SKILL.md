---
name: knowledge-db
description: ナレッジDB（GitHub リポジトリ EraX-1/knowledge_db）に知識を蓄積・整理するためのスキル。ユーザーが「ナレッジDBに蓄積して」「ナレッジに記録して」「知識ベースに追加して」「ナレッジDBに書き込んで」「学んだことを記録して」などと言ったときに使用する。会話中に得られた技術的知見、トラブルシュートの結果、新しい手法の発見などを構造化してリポジトリに保存する。
---

# ナレッジDB蓄積スキル

## リポジトリ情報

- **リポジトリ**: `EraX-1/knowledge_db`
- **ブランチ**: `main`（直接コミット・push）
- **操作手段**: `gh` CLIコマンド

## ワークフロー

ナレッジを蓄積する際は、以下の手順を必ず順番に実行する。

### 1. 蓄積する知識の整理

会話の文脈から、蓄積すべき知識を特定する。以下を明確にする：

- **何についての知識か**（タイトル）
- **どの技術領域に属するか**（大分類・小分類）
- **具体的な内容**（概要、使い方、課題、効果、コード例）

### 2. 既存ディレクトリ構造の確認

リポジトリの現在のディレクトリ構造を必ず確認する。推測で分類先を決めてはいけない。

```bash
gh api 'repos/EraX-1/knowledge_db/git/trees/main?recursive=1' --jq '.tree[] | select(.type=="tree") | .path'
```

これにより、既存の大分類・小分類をすべて把握する。

### 3. 分類先の決定

既存の構造を確認した上で、知識の分類先を決める：

- **既存の小分類に該当する場合** → そのディレクトリに配置
- **既存の大分類に該当するが小分類がない場合** → 新しい小分類ディレクトリを作成
- **どの大分類にも該当しない場合** → 新しい大分類と小分類を作成

ディレクトリ名は英語の小文字、ハイフン区切りで命名する（例: `ai/rag`, `devops/ci-cd`）。

### 4. 既存ファイルの確認

分類先ディレクトリ内に同じトピックのファイルが既にないか確認する：

```bash
gh api 'repos/EraX-1/knowledge_db/git/trees/main?recursive=1' --jq '.tree[] | select(.path | startswith("<分類パス>/")) | .path'
```

- **同じトピックのファイルが存在する場合** → 既存ファイルの内容を取得し、追記・更新する
- **存在しない場合** → 新規ファイルを作成する

### 5. テンプレートの取得

新規ファイルを作成する場合、リポジトリ直下の `TEMPLATE.md` を必ず取得し、その構成に従ってファイルを作成する：

```bash
gh api 'repos/EraX-1/knowledge_db/contents/TEMPLATE.md' --jq '.content' | base64 -d
```

テンプレートの各セクション（概要、使い方、発生した課題と対処方法、効果、サンプルコード）をすべて含めること。該当する内容がないセクションには「N/A」と記載する。

### 6. ファイルの作成・更新

`gh api` を使ってファイルを作成または更新する。

#### 新規作成の場合

```bash
CONTENT=$(cat <<'DOCEOF'
（テンプレートに従ったMarkdown内容）
DOCEOF
)

gh api --method PUT "repos/EraX-1/knowledge_db/contents/<分類パス>/<ファイル名>.md" \
  -f message="add: <分類パス>/<ファイル名> のナレッジを追加" \
  -f content="$(echo "$CONTENT" | base64)"
```

#### 既存ファイル更新の場合

```bash
# まずSHAを取得
SHA=$(gh api "repos/EraX-1/knowledge_db/contents/<ファイルパス>" --jq '.sha')

gh api --method PUT "repos/EraX-1/knowledge_db/contents/<ファイルパス>" \
  -f message="update: <ファイルパス> のナレッジを更新" \
  -f content="$(echo "$CONTENT" | base64)" \
  -f sha="$SHA"
```

### 7. 結果の報告

完了後、ユーザーに以下を報告する：

- 保存先パス（例: `ai/rag/hyde.md`）
- 新規作成か更新かの区別
- GitHubでの閲覧リンク: `https://github.com/EraX-1/knowledge_db/blob/main/<パス>`

## ファイル命名規則

- 英語の小文字、ハイフン区切り（例: `small-to-big-retrieval.md`）
- 内容を端的に表す名前にする
- 1トピック = 1ファイル

## ディレクトリ構造の例

```
knowledge_db/
├── TEMPLATE.md
├── ai/
│   ├── rag/
│   │   ├── hyde.md
│   │   ├── small-to-big-retrieval.md
│   │   └── chunking-strategies.md
│   ├── ocr/
│   │   └── azure-document-intelligence.md
│   └── llm/
│       └── prompt-engineering.md
├── devops/
│   ├── ci-cd/
│   │   └── github-actions.md
│   └── deployment/
│       └── azure-vm-deploy.md
└── database/
    ├── mongodb/
    │   └── schema-design.md
    └── mysql/
        └── migration-tips.md
```

## 重要なルール

1. **構造を必ず確認してから分類する** — 推測でディレクトリを決めない
2. **テンプレートに従う** — 新規ファイルは必ず `TEMPLATE.md` の構成を使用する
3. **既存ファイルとの重複を避ける** — 同じトピックがあれば追記・更新する
4. **コミットメッセージは日本語で意味がわかるように** — `add:` / `update:` プレフィックスを使う
5. **作成後にリンクを報告する** — ユーザーが確認できるようにする
