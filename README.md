# Knowledge DB Plugin

ナレッジDB（EraX-1/knowledge_db）への読み書きを行う Claude Code プラグインです。

## 前提条件

- [Claude Code](https://claude.com/claude-code) がインストール済み
- [GitHub CLI (gh)](https://cli.github.com/) がインストール・ログイン済み
  ```bash
  # インストール（Mac）
  brew install gh
  # ログイン
  gh auth login
  ```
- `EraX-1/knowledge_db` リポジトリへのアクセス権（read/write）

## インストール

Claude Code 上で以下を実行してください：

```
/plugin marketplace add EraX-1/knowledge-db-plugin
/plugin install knowledge-db-plugin@erax-tools
```

## 使い方

### 書き込み（蓄積）

会話中に得た知見を保存したいときに使います。

```
「ナレッジDBに蓄積して」
「この知識をナレッジに記録して」
```

→ 自動で分類・テンプレート適用・コミットまで行います。

### 読み取り（検索・取得）

蓄積済みのナレッジを引き出して回答に活用します。

```
「ナレッジDBから取得して」
「ナレッジを参照して教えて」
```

→ リポジトリを探索し、関連するナレッジを取得して回答します。

## ナレッジの構造

```
knowledge_db/
├── TEMPLATE.md        # ナレッジテンプレート
├── ai/
│   ├── rag/           # RAG関連
│   ├── speech-to-text/ # 文字起こし関連
│   └── llm/           # LLM関連
├── devops/
│   └── deployment/
└── database/
    ├── mongodb/
    └── mysql/
```

各ナレッジは「概要・使い方・課題と対処・効果・サンプルコード」の統一フォーマットで記録されます。
