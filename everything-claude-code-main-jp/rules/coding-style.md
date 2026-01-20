# コーディングスタイル

## イミュータビリティ（重要）

常に新しいオブジェクトを作成、絶対にミューテートしない:

```javascript
// 間違い: ミューテーション
function updateUser(user, name) {
  user.name = name  // ミューテーション！
  return user
}

// 正しい: イミュータビリティ
function updateUser(user, name) {
  return {
    ...user,
    name
  }
}
```

## ファイル構成

多数の小さなファイル > 少数の大きなファイル:
- 高凝集・低結合
- 通常200-400行、最大800行
- 大きなコンポーネントからユーティリティを抽出
- タイプ別ではなく、機能/ドメイン別に整理

## エラーハンドリング

常に包括的にエラーを処理:

```typescript
try {
  const result = await riskyOperation()
  return result
} catch (error) {
  console.error('操作が失敗しました:', error)
  throw new Error('ユーザーフレンドリーな詳細メッセージ')
}
```

## 入力バリデーション

常にユーザー入力をバリデート:

```typescript
import { z } from 'zod'

const schema = z.object({
  email: z.string().email(),
  age: z.number().int().min(0).max(150)
})

const validated = schema.parse(input)
```

## コード品質チェックリスト

作業完了前に確認:
- [ ] コードが読みやすく適切に命名されている
- [ ] 関数が小さい（50行未満）
- [ ] ファイルが焦点を絞っている（800行未満）
- [ ] 深いネストがない（4レベル超）
- [ ] 適切なエラーハンドリング
- [ ] console.log文がない
- [ ] ハードコードされた値がない
- [ ] ミューテーションがない（イミュータブルパターンを使用）
