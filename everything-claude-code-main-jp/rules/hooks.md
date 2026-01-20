# フックシステム

## フックの種類

- **PreToolUse**: ツール実行前（バリデーション、パラメータ変更）
- **PostToolUse**: ツール実行後（自動フォーマット、チェック）
- **Stop**: セッション終了時（最終確認）

## 現在のフック（~/.claude/settings.json内）

### PreToolUse
- **tmuxリマインダー**: 長時間実行コマンド（npm、pnpm、yarn、cargo等）にtmuxを提案
- **git pushレビュー**: プッシュ前にZedでレビューを開く
- **docブロッカー**: 不要な.md/.txtファイルの作成をブロック

### PostToolUse
- **PR作成**: PRのURLとGitHub Actionsステータスをログ
- **Prettier**: 編集後にJS/TSファイルを自動フォーマット
- **TypeScriptチェック**: .ts/.tsxファイル編集後にtscを実行
- **console.log警告**: 編集されたファイルのconsole.logについて警告

### Stop
- **console.log監査**: セッション終了前にすべての変更ファイルでconsole.logをチェック

## 自動承認権限

注意して使用:
- 信頼できる、明確に定義された計画に対して有効化
- 探索的な作業では無効化
- dangerously-skip-permissionsフラグは絶対に使用しない
- 代わりに`~/.claude.json`で`allowedTools`を設定

## TodoWriteベストプラクティス

TodoWriteツールを使用して:
- マルチステップタスクの進捗を追跡
- 指示の理解を確認
- リアルタイムのステアリングを可能に
- 詳細な実装ステップを表示

Todoリストが明らかにするもの:
- 順序が間違っているステップ
- 欠けているアイテム
- 余分な不要なアイテム
- 間違った粒度
- 誤解された要件
