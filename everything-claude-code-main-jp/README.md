# Everything Claude Code 日本語版

**Anthropicハッカソン優勝者によるClaude Code設定の完全コレクション**

> **📋 クレジット・著作権表示**
> 
> このリポジトリは [affaan-m/everything-claude-code](https://github.com/affaan-m/everything-claude-code) の日本語翻訳版です。
> 
> - **オリジナル著作者**: Affaan Mustafa ([@affaanmustafa](https://x.com/affaanmustafa))
> - **ライセンス**: MIT License
> - **翻訳**: t2k2pp
> 
> オリジナルリポジトリに感謝します。

このリポジトリには、Claude Codeで日常的に使用している本番環境対応のエージェント、スキル、フック、コマンド、ルール、MCP設定が含まれています。これらの設定は、10ヶ月以上にわたる実際の製品開発での集中的な使用を通じて進化しました。

---

## まず完全ガイドを読んでください

**これらの設定に取り組む前に、Xの完全ガイドを読んでください:**

**[The Shorthand Guide to Everything Claude Code](https://x.com/affaanmustafa/status/2012378465664745795)**

ガイドでは以下を説明しています:
- 各設定タイプの機能と使用タイミング
- Claude Codeセットアップの構造化方法
- コンテキストウィンドウ管理（パフォーマンスに重要）
- 並列ワークフローと高度なテクニック
- これらの設定の背後にある思想

**このリポジトリは設定のみです！ヒント、コツ、その他の例は私のX記事と動画にあります。**

---

## 内容

```
everything-claude-code-jp/
|-- agents/           # 委任用の専門サブエージェント
|   |-- planner.md           # 機能実装計画
|   |-- architect.md         # システム設計判断
|   |-- tdd-guide.md         # テスト駆動開発
|   |-- code-reviewer.md     # 品質・セキュリティレビュー
|   |-- security-reviewer.md # 脆弱性分析
|   |-- build-error-resolver.md
|   |-- e2e-runner.md        # Playwright E2Eテスト
|   |-- refactor-cleaner.md  # 不要コード削除
|   |-- doc-updater.md       # ドキュメント同期
|
|-- skills/           # ワークフロー定義とドメイン知識
|   |-- coding-standards.md         # 言語ベストプラクティス
|   |-- backend-patterns.md         # API、データベース、キャッシュパターン
|   |-- frontend-patterns.md        # React、Next.jsパターン
|   |-- project-guidelines-example.md # プロジェクト固有スキル例
|   |-- tdd-workflow/               # TDD方法論
|   |-- security-review/            # セキュリティチェックリスト
|   |-- clickhouse-io.md            # ClickHouse分析
|
|-- commands/         # クイック実行用スラッシュコマンド
|   |-- tdd.md              # /tdd - テスト駆動開発
|   |-- plan.md             # /plan - 実装計画
|   |-- e2e.md              # /e2e - E2Eテスト生成
|   |-- code-review.md      # /code-review - 品質レビュー
|   |-- build-fix.md        # /build-fix - ビルドエラー修正
|   |-- refactor-clean.md   # /refactor-clean - 不要コード削除
|   |-- test-coverage.md    # /test-coverage - カバレッジ分析
|   |-- update-codemaps.md  # /update-codemaps - ドキュメント更新
|   |-- update-docs.md      # /update-docs - ドキュメント同期
|
|-- rules/            # 常に従うべきガイドライン
|   |-- security.md         # 必須セキュリティチェック
|   |-- coding-style.md     # イミュータビリティ、ファイル構成
|   |-- testing.md          # TDD、80%カバレッジ要件
|   |-- git-workflow.md     # コミット形式、PRプロセス
|   |-- agents.md           # サブエージェントへの委任タイミング
|   |-- performance.md      # モデル選択、コンテキスト管理
|   |-- patterns.md         # APIレスポンス形式、フック
|   |-- hooks.md            # フックのドキュメント
|
|-- hooks/            # トリガーベースの自動化
|   |-- hooks.json          # PreToolUse、PostToolUse、Stopフック
|
|-- mcp-configs/      # MCPサーバー設定
|   |-- mcp-servers.json    # GitHub、Supabase、Vercel、Railway等
|
|-- plugins/          # プラグインエコシステムドキュメント
|   |-- README.md           # プラグイン、マーケットプレイス、スキルガイド
|
|-- examples/         # 設定例
    |-- CLAUDE.md           # プロジェクトレベル設定例
    |-- user-CLAUDE.md      # ユーザーレベル設定例 (~/.claude/CLAUDE.md)
    |-- statusline.json     # カスタムステータスライン設定
```

---

## クイックスタート

### 1. 必要なものをコピー

```bash
# リポジトリをクローン
git clone https://github.com/affaan-m/everything-claude-code.git

# エージェントをClaude設定にコピー
cp everything-claude-code/agents/*.md ~/.claude/agents/

# ルールをコピー
cp everything-claude-code/rules/*.md ~/.claude/rules/

# コマンドをコピー
cp everything-claude-code/commands/*.md ~/.claude/commands/

# スキルをコピー
cp -r everything-claude-code/skills/* ~/.claude/skills/
```

### 2. settings.jsonにフックを追加

`hooks/hooks.json`のフックを`~/.claude/settings.json`にコピーしてください。

### 3. MCPを設定

`mcp-configs/mcp-servers.json`から必要なMCPサーバーを`~/.claude.json`にコピーしてください。

**重要:** `YOUR_*_HERE`プレースホルダーを実際のAPIキーに置き換えてください。

### 4. ガイドを読む

本気で、[ガイドを読んでください](https://x.com/affaanmustafa/status/2012378465664745795)。これらの設定はコンテキストがあると10倍理解しやすくなります。

---

## 主要コンセプト

### エージェント

サブエージェントは限定されたスコープで委任されたタスクを処理します。例:

```markdown
---
name: code-reviewer
description: 品質、セキュリティ、保守性のコードレビュー専門家
tools: Read, Grep, Glob, Bash
model: opus
---

あなたはシニアコードレビュアーです...
```

### スキル

スキルはコマンドやエージェントによって呼び出されるワークフロー定義です:

```markdown
# TDDワークフロー

1. まずインターフェースを定義
2. 失敗するテストを書く（RED）
3. 最小限のコードを実装（GREEN）
4. リファクタリング（IMPROVE）
5. 80%以上のカバレッジを確認
```

### フック

フックはツールイベント時に発火します。例 - console.logの警告:

```json
{
  "matcher": "tool == \"Edit\" && tool_input.file_path matches \"\\\\.(ts|tsx|js|jsx)$\"",
  "hooks": [{
    "type": "command",
    "command": "#!/bin/bash\ngrep -n 'console\\.log' \"$file_path\" && echo '[Hook] Remove console.log' >&2"
  }]
}
```

### ルール

ルールは常に従うべきガイドラインです。モジュール化して保持:

```
~/.claude/rules/
  security.md      # 秘密キーのハードコード禁止
  coding-style.md  # イミュータビリティ、ファイル制限
  testing.md       # TDD、カバレッジ要件
```

---

## コントリビュート

**コントリビュートは歓迎され、推奨されています。**

このリポジトリはコミュニティリソースを目的としています。以下をお持ちの方:
- 便利なエージェントやスキル
- 巧妙なフック
- より良いMCP設定
- 改善されたルール

ぜひコントリビュートしてください！ガイドラインは[CONTRIBUTING.md](CONTRIBUTING.md)を参照してください。

### コントリビュートのアイデア

- 言語固有のスキル（Python、Go、Rustパターン）
- フレームワーク固有の設定（Django、Rails、Laravel）
- DevOpsエージェント（Kubernetes、Terraform、AWS）
- テスト戦略（各種フレームワーク）
- ドメイン固有の知識（ML、データエンジニアリング、モバイル）

---

## 背景

私は実験的ロールアウト以来、Claude Codeを使用しています。2025年9月のAnthropic x Forum Venturesハッカソンで[@DRodriguezFX](https://x.com/DRodriguezFX)と共に[zenith.chat](https://zenith.chat)を構築し、完全にClaude Codeを使用して優勝しました。

これらの設定は複数の本番アプリケーションで実戦テスト済みです。

---

## 重要な注意事項

### コンテキストウィンドウ管理

**重要:** すべてのMCPを同時に有効化しないでください。あまりにも多くのツールを有効にすると、200kのコンテキストウィンドウが70kまで縮小する可能性があります。

経験則:
- 20-30のMCPを設定
- プロジェクトあたり10未満を有効化
- 80未満のツールをアクティブに

プロジェクト設定の`disabledMcpServers`を使用して未使用のものを無効化してください。

### カスタマイズ

これらの設定は私のワークフローに合わせています。あなたは:
1. 共感できるものから始める
2. スタックに合わせて変更
3. 使用しないものを削除
4. 独自のパターンを追加

---

## リンク

- **完全ガイド:** [The Shorthand Guide to Everything Claude Code](https://x.com/affaanmustafa/status/2012378465664745795)
- **フォロー:** [@affaanmustafa](https://x.com/affaanmustafa)
- **zenith.chat:** [zenith.chat](https://zenith.chat)

---

## ライセンス

MIT - 自由に使用、必要に応じて変更、可能であればコントリビュートバック。

---

**役に立ったらスターを。ガイドを読んで。素晴らしいものを作ろう。**
