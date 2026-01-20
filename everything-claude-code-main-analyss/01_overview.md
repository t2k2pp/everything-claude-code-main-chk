# Everything Claude Code 分析レポート

## 概要

「Everything Claude Code」は、Anthropic ハッカソン優勝者が作成した Claude Code 設定ファイルの包括的なコレクションです。本番環境で使用可能なエージェント、スキル、フック、コマンド、ルール、MCP設定が含まれています。

## リポジトリ構造

```
everything-claude-code/
├── agents/       # 9つの専門エージェント
├── commands/     # 9つのスラッシュコマンド
├── rules/        # 8つのルールファイル
├── skills/       # 4つのスキルファイル
├── examples/     # 設定例（3ファイル）
├── hooks/        # フック設定
├── mcp-configs/  # MCPサーバー設定
└── plugins/      # プラグインガイド
```

## 主要コンポーネント

### エージェント（~/.claude/agents/）
- **planner** - 実装計画立案
- **architect** - システム設計
- **tdd-guide** - テスト駆動開発
- **code-reviewer** - コードレビュー
- **security-reviewer** - セキュリティ分析
- **build-error-resolver** - ビルドエラー修正
- **e2e-runner** - E2Eテスト実行
- **refactor-cleaner** - 不要コード削除
- **doc-updater** - ドキュメント更新

### コマンド（/plan, /tdd, /code-review など）
ユーザーがスラッシュコマンドで呼び出せるエージェント

### ルール
セキュリティ、コーディングスタイル、テストなどのガイドライン

## 対象ユーザー

- Claude Code を日常的に使用する開発者
- AIペアプログラミングを活用したいチーム
- 品質とセキュリティを重視するプロジェクト

## 関連ドキュメント

- [メリット・デメリット分析](./02_merits_demerits.md)
- [コンポーネント詳細](./03_components_detail.md)
- [重要ポイント](./04_key_points.md)
- [利用ガイド](./05_usage_guide.md)
- [カスタマイズ例](./06_customization.md)
