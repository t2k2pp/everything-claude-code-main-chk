---
name: coding-standards
description: TypeScript、JavaScript、React、Node.js開発のための汎用コーディング標準、ベストプラクティス、パターン。
---

# コーディング標準＆ベストプラクティス

すべてのプロジェクトに適用される汎用コーディング標準。

## コード品質の原則

### 1. 可読性第一
- コードは書くよりも読まれることが多い
- 明確な変数名と関数名
- コメントよりも自己文書化コードを優先
- 一貫したフォーマット

### 2. KISS（シンプルに保つ）
- 機能する最もシンプルな解決策
- 過剰エンジニアリングを避ける
- 早すぎる最適化はしない
- 賢いコードより理解しやすいコード

### 3. DRY（繰り返さない）
- 共通ロジックを関数に抽出
- 再利用可能なコンポーネントを作成
- モジュール間でユーティリティを共有
- コピーペーストプログラミングを避ける

### 4. YAGNI（必要になるまで作らない）
- 必要になる前に機能を作らない
- 投機的な一般化を避ける
- 必要な時にのみ複雑さを追加
- シンプルに始め、必要時にリファクタリング

## TypeScript/JavaScript標準

### 変数の命名

```typescript
// ✅ 良い: 説明的な名前
const marketSearchQuery = 'election'
const isUserAuthenticated = true
const totalRevenue = 1000

// ❌ 悪い: 不明確な名前
const q = 'election'
const flag = true
const x = 1000
```

### 関数の命名

```typescript
// ✅ 良い: 動詞-名詞パターン
async function fetchMarketData(marketId: string) { }
function calculateSimilarity(a: number[], b: number[]) { }
function isValidEmail(email: string): boolean { }

// ❌ 悪い: 不明確または名詞のみ
async function market(id: string) { }
function similarity(a, b) { }
function email(e) { }
```

### イミュータビリティパターン（重要）

```typescript
// ✅ 常にスプレッド演算子を使用
const updatedUser = {
  ...user,
  name: '新しい名前'
}

const updatedArray = [...items, newItem]

// ❌ 絶対に直接ミューテートしない
user.name = '新しい名前'  // 悪い
items.push(newItem)       // 悪い
```

### エラーハンドリング

```typescript
// ✅ 良い: 包括的なエラーハンドリング
async function fetchData(url: string) {
  try {
    const response = await fetch(url)

    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`)
    }

    return await response.json()
  } catch (error) {
    console.error('取得に失敗しました:', error)
    throw new Error('データの取得に失敗しました')
  }
}

// ❌ 悪い: エラーハンドリングなし
async function fetchData(url) {
  const response = await fetch(url)
  return response.json()
}
```

### 型安全性

```typescript
// ✅ 良い: 適切な型
interface Market {
  id: string
  name: string
  status: 'active' | 'resolved' | 'closed'
  created_at: Date
}

function getMarket(id: string): Promise<Market> {
  // 実装
}

// ❌ 悪い: 'any'を使用
function getMarket(id: any): Promise<any> {
  // 実装
}
```

## Reactベストプラクティス

### コンポーネント構造

```typescript
// ✅ 良い: 型付き関数コンポーネント
interface ButtonProps {
  children: React.ReactNode
  onClick: () => void
  disabled?: boolean
  variant?: 'primary' | 'secondary'
}

export function Button({
  children,
  onClick,
  disabled = false,
  variant = 'primary'
}: ButtonProps) {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={`btn btn-${variant}`}
    >
      {children}
    </button>
  )
}
```

### カスタムフック

```typescript
// ✅ 良い: 再利用可能なカスタムフック
export function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value)

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value)
    }, delay)

    return () => clearTimeout(handler)
  }, [value, delay])

  return debouncedValue
}

// 使用法
const debouncedQuery = useDebounce(searchQuery, 500)
```

## API設計標準

### RESTful API規約

```
GET    /api/markets              # 全マーケットをリスト
GET    /api/markets/:id          # 特定のマーケットを取得
POST   /api/markets              # 新しいマーケットを作成
PUT    /api/markets/:id          # マーケットを更新（完全）
PATCH  /api/markets/:id          # マーケットを更新（部分）
DELETE /api/markets/:id          # マーケットを削除

# フィルタリング用クエリパラメータ
GET /api/markets?status=active&limit=10&offset=0
```

### レスポンス形式

```typescript
// ✅ 良い: 一貫したレスポンス構造
interface ApiResponse<T> {
  success: boolean
  data?: T
  error?: string
  meta?: {
    total: number
    page: number
    limit: number
  }
}
```

### 入力バリデーション

```typescript
import { z } from 'zod'

// ✅ 良い: スキーマバリデーション
const CreateMarketSchema = z.object({
  name: z.string().min(1).max(200),
  description: z.string().min(1).max(2000),
  endDate: z.string().datetime(),
  categories: z.array(z.string()).min(1)
})
```

## ファイル構成

### プロジェクト構造

```
src/
├── app/                    # Next.js App Router
│   ├── api/               # APIルート
│   ├── markets/           # マーケットページ
│   └── (auth)/           # 認証ページ（ルートグループ）
├── components/            # Reactコンポーネント
│   ├── ui/               # 汎用UIコンポーネント
│   ├── forms/            # フォームコンポーネント
│   └── layouts/          # レイアウトコンポーネント
├── hooks/                # カスタムReactフック
├── lib/                  # ユーティリティと設定
├── types/                # TypeScript型
└── styles/              # グローバルスタイル
```

## コメント＆ドキュメント

### コメントのタイミング

```typescript
// ✅ 良い: WHYを説明、WHATではない
// 停止中のAPIを圧倒しないようにエクスポネンシャルバックオフを使用
const delay = Math.min(1000 * Math.pow(2, retryCount), 30000)

// 大きな配列のパフォーマンスのため意図的にミューテーションを使用
items.push(newItem)

// ❌ 悪い: 明白なことを述べる
// カウンターを1増加
count++
```

## テスト標準

### テスト構造（AAAパターン）

```typescript
test('類似性を正しく計算する', () => {
  // Arrange（準備）
  const vector1 = [1, 0, 0]
  const vector2 = [0, 1, 0]

  // Act（実行）
  const similarity = calculateCosineSimilarity(vector1, vector2)

  // Assert（検証）
  expect(similarity).toBe(0)
})
```

## コードの臭い検出

注意すべきアンチパターン:

### 1. 長い関数
```typescript
// ❌ 悪い: 50行以上の関数
function processMarketData() {
  // 100行のコード
}

// ✅ 良い: 小さな関数に分割
function processMarketData() {
  const validated = validateData()
  const transformed = transformData(validated)
  return saveData(transformed)
}
```

### 2. 深いネスト
```typescript
// ❌ 悪い: 5レベル以上のネスト
if (user) {
  if (user.isAdmin) {
    if (market) {
      // ...
    }
  }
}

// ✅ 良い: 早期リターン
if (!user) return
if (!user.isAdmin) return
if (!market) return

// 処理を実行
```

### 3. マジックナンバー
```typescript
// ❌ 悪い: 説明のない数字
if (retryCount > 3) { }

// ✅ 良い: 名前付き定数
const MAX_RETRIES = 3
if (retryCount > MAX_RETRIES) { }
```

**覚えておいてください**: コード品質は妥協の余地がありません。明確で保守しやすいコードは、迅速な開発と自信を持ったリファクタリングを可能にします。
