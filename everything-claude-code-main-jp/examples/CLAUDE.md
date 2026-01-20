# プロジェクトCLAUDE.mdの例

これはプロジェクトレベルのCLAUDE.mdファイルの例です。プロジェクトルートに配置してください。

## プロジェクト概要

[プロジェクトの簡単な説明 - 何をするか、技術スタック]

## 重要なルール

### 1. コード構成

- 少数の大きなファイルより多数の小さなファイル
- 高凝集・低結合
- 通常200-400行、ファイルあたり最大800行
- タイプ別ではなく機能/ドメイン別に整理

### 2. コードスタイル

- コード、コメント、ドキュメントで絵文字なし
- 常にイミュータビリティ - オブジェクトや配列を絶対にミューテートしない
- 本番コードでconsole.logなし
- try/catchで適切なエラーハンドリング
- Zodなどで入力バリデーション

### 3. テスト

- TDD: まずテストを書く
- 最小80%カバレッジ
- ユーティリティのユニットテスト
- APIの統合テスト
- 重要なフローのE2Eテスト

### 4. セキュリティ

- ハードコードされたシークレットなし
- 機密データは環境変数
- すべてのユーザー入力をバリデート
- パラメータ化クエリのみ
- CSRF保護有効

## ファイル構造

```
src/
|-- app/              # Next.js app router
|-- components/       # 再利用可能なUIコンポーネント
|-- hooks/            # カスタムReactフック
|-- lib/              # ユーティリティライブラリ
|-- types/            # TypeScript定義
```

## キーパターン

### APIレスポンス形式

```typescript
interface ApiResponse<T> {
  success: boolean
  data?: T
  error?: string
}
```

### エラーハンドリング

```typescript
try {
  const result = await operation()
  return { success: true, data: result }
} catch (error) {
  console.error('操作に失敗しました:', error)
  return { success: false, error: 'ユーザーフレンドリーなメッセージ' }
}
```

## 環境変数

```bash
# 必須
DATABASE_URL=
API_KEY=

# オプション
DEBUG=false
```

## 利用可能なコマンド

- `/tdd` - テスト駆動開発ワークフロー
- `/plan` - 実装計画を作成
- `/code-review` - コード品質をレビュー
- `/build-fix` - ビルドエラーを修正

## Gitワークフロー

- Conventional Commits: `feat:`, `fix:`, `refactor:`, `docs:`, `test:`
- mainに直接コミットしない
- PRはレビューが必要
- マージ前にすべてのテストが成功必須
