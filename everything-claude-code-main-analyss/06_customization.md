# カスタマイズ例

## 1. 言語固有のエージェント追加

### Python専用コードレビュアー

```markdown
---
name: python-reviewer
description: Python専用のコードレビュースペシャリスト。PEP 8、型ヒント、Pythonベストプラクティスをチェック。
tools: Read, Grep, Bash
model: sonnet
---

# Python Code Reviewer

Pythonコードの品質レビューを行うスペシャリスト。

## チェック項目

1. **PEP 8 準拠**
   - インデント（4スペース）
   - 行長（79文字）
   - 命名規則

2. **型ヒント**
   - 関数シグネチャ
   - 変数アノテーション

3. **Pythonベストプラクティス**
   - リスト内包表記の適切な使用
   - context manager（with文）
   - f-string

## コマンド
\`\`\`bash
# 型チェック
mypy --strict path/to/file.py

# リント
ruff check .

# フォーマット
black --check .
\`\`\`
```

---

## 2. フレームワーク固有のスキル

### Django プロジェクトスキル

```markdown
---
name: django-patterns
description: Djangoプロジェクトのパターンとベストプラクティス
---

# Django Patterns

## モデル設計

\`\`\`python
from django.db import models

class BaseModel(models.Model):
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        abstract = True
\`\`\`

## ビューパターン

- CBV（Class-Based Views）を優先
- 認証には@login_requiredデコレータ
- permissionはdjango-guardianで管理

## テスト

\`\`\`bash
pytest --cov=. --cov-report=html
\`\`\`
```

---

## 3. プロジェクト固有のCLAUDE.md

### 金融アプリケーション例

```markdown
# 金融アプリ CLAUDE.md

## セキュリティ要件（必須）

- すべての金額計算はDecimalを使用（floatは禁止）
- 取引ログは必ず記録
- 2FA必須のエンドポイントを明示
- PCI DSSコンプライアンスを考慮

## データベース

- 金額フィールドはNUMERIC(19,4)
- 取引は必ずトランザクション内
- 楽観的ロックを使用

## テスト

- 金額計算: 100%カバレッジ必須
- 境界値テスト必須
- 並行処理テスト必須

## 禁止事項

- console.logで金額情報を出力しない
- URLパラメータに金額を含めない
- クライアントサイドで金額計算しない
```

---

## 4. カスタムフックの追加

### ESLint自動修正フック

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "tool == \"Edit\" && tool_input.file_path matches \"\\\\.(ts|tsx)$\"",
        "hooks": [
          {
            "type": "command",
            "command": "#!/bin/bash\nfile_path=$(cat | jq -r '.tool_input.file_path')\nif [ -f \"$file_path\" ]; then\n  npx eslint --fix \"$file_path\" 2>&1\nfi"
          }
        ],
        "description": "TS/TSXファイル編集後にESLint自動修正"
      }
    ]
  }
}
```

---

## 5. MCP サーバーの追加

### Slack通知MCP

```json
{
  "mcpServers": {
    "slack": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "xoxb-..."
      },
      "description": "Slack通知送信"
    }
  }
}
```

---

## 6. チーム向け標準化

### チーム共有設定

```
project/
├── .claude/
│   ├── CLAUDE.md           # プロジェクト設定
│   ├── agents/
│   │   └── team-reviewer.md # チーム固有レビュアー
│   ├── rules/
│   │   └── team-rules.md   # チームルール
│   └── skills/
│       └── our-stack.md    # 使用技術スタック
└── ...
```

### チームルール例

```markdown
# チームルール

## コードレビュー

- PRは24時間以内にレビュー
- 2人以上の承認が必要
- テストカバレッジが下がるPRは却下

## コミット

- feat/fix/refactor/docs/test/chore を使用
- 日本語メッセージ禁止（英語のみ）
- 1コミット1機能

## ブランチ

- main: 本番
- develop: 開発
- feature/xxx: 機能開発
- fix/xxx: バグ修正
```
