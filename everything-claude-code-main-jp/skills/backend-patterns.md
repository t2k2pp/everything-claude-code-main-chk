---
name: backend-patterns
description: Node.js、Express、Next.js APIルートのためのバックエンドアーキテクチャパターン、API設計、データベース最適化、サーバーサイドベストプラクティス。
---

# バックエンド開発パターン

スケーラブルなサーバーサイドアプリケーションのためのバックエンドアーキテクチャパターンとベストプラクティス。

## API設計パターン

### RESTful API構造

```typescript
// ✅ リソースベースのURL
GET    /api/markets                 # リソースをリスト
GET    /api/markets/:id             # 単一リソースを取得
POST   /api/markets                 # リソースを作成
PUT    /api/markets/:id             # リソースを置換
PATCH  /api/markets/:id             # リソースを更新
DELETE /api/markets/:id             # リソースを削除

// ✅ フィルタリング、ソート、ページネーション用クエリパラメータ
GET /api/markets?status=active&sort=volume&limit=20&offset=0
```

### リポジトリパターン

```typescript
// データアクセスロジックを抽象化
interface MarketRepository {
  findAll(filters?: MarketFilters): Promise<Market[]>
  findById(id: string): Promise<Market | null>
  create(data: CreateMarketDto): Promise<Market>
  update(id: string, data: UpdateMarketDto): Promise<Market>
  delete(id: string): Promise<void>
}

class SupabaseMarketRepository implements MarketRepository {
  async findAll(filters?: MarketFilters): Promise<Market[]> {
    let query = supabase.from('markets').select('*')

    if (filters?.status) {
      query = query.eq('status', filters.status)
    }

    const { data, error } = await query
    if (error) throw new Error(error.message)
    return data
  }
}
```

### サービスレイヤーパターン

```typescript
// ビジネスロジックをデータアクセスから分離
class MarketService {
  constructor(private marketRepo: MarketRepository) {}

  async searchMarkets(query: string, limit: number = 10): Promise<Market[]> {
    // ビジネスロジック
    const embedding = await generateEmbedding(query)
    const results = await this.vectorSearch(embedding, limit)

    // 完全なデータを取得
    const markets = await this.marketRepo.findByIds(results.map(r => r.id))

    // 類似度でソート
    return markets.sort((a, b) => {
      const scoreA = results.find(r => r.id === a.id)?.score || 0
      const scoreB = results.find(r => r.id === b.id)?.score || 0
      return scoreA - scoreB
    })
  }
}
```

### ミドルウェアパターン

```typescript
// リクエスト/レスポンス処理パイプライン
export function withAuth(handler: NextApiHandler): NextApiHandler {
  return async (req, res) => {
    const token = req.headers.authorization?.replace('Bearer ', '')

    if (!token) {
      return res.status(401).json({ error: '未認証' })
    }

    try {
      const user = await verifyToken(token)
      req.user = user
      return handler(req, res)
    } catch (error) {
      return res.status(401).json({ error: '無効なトークン' })
    }
  }
}
```

## データベースパターン

### クエリ最適化

```typescript
// ✅ 良い: 必要なカラムのみ選択
const { data } = await supabase
  .from('markets')
  .select('id, name, status, volume')
  .eq('status', 'active')
  .order('volume', { ascending: false })
  .limit(10)

// ❌ 悪い: すべてを選択
const { data } = await supabase
  .from('markets')
  .select('*')
```

### N+1クエリ防止

```typescript
// ❌ 悪い: N+1クエリ問題
const markets = await getMarkets()
for (const market of markets) {
  market.creator = await getUser(market.creator_id)  // Nクエリ
}

// ✅ 良い: バッチ取得
const markets = await getMarkets()
const creatorIds = markets.map(m => m.creator_id)
const creators = await getUsers(creatorIds)  // 1クエリ
const creatorMap = new Map(creators.map(c => [c.id, c]))

markets.forEach(market => {
  market.creator = creatorMap.get(market.creator_id)
})
```

## キャッシング戦略

### Redisキャッシングレイヤー

```typescript
class CachedMarketRepository implements MarketRepository {
  constructor(
    private baseRepo: MarketRepository,
    private redis: RedisClient
  ) {}

  async findById(id: string): Promise<Market | null> {
    // まずキャッシュをチェック
    const cached = await this.redis.get(`market:${id}`)

    if (cached) {
      return JSON.parse(cached)
    }

    // キャッシュミス - データベースから取得
    const market = await this.baseRepo.findById(id)

    if (market) {
      // 5分間キャッシュ
      await this.redis.setex(`market:${id}`, 300, JSON.stringify(market))
    }

    return market
  }
}
```

## エラーハンドリングパターン

### 集中エラーハンドラー

```typescript
class ApiError extends Error {
  constructor(
    public statusCode: number,
    public message: string,
    public isOperational = true
  ) {
    super(message)
  }
}

export function errorHandler(error: unknown, req: Request): Response {
  if (error instanceof ApiError) {
    return NextResponse.json({
      success: false,
      error: error.message
    }, { status: error.statusCode })
  }

  // 予期しないエラーをログ
  console.error('予期しないエラー:', error)

  return NextResponse.json({
    success: false,
    error: '内部サーバーエラー'
  }, { status: 500 })
}
```

### エクスポネンシャルバックオフでリトライ

```typescript
async function fetchWithRetry<T>(
  fn: () => Promise<T>,
  maxRetries = 3
): Promise<T> {
  let lastError: Error

  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn()
    } catch (error) {
      lastError = error as Error

      if (i < maxRetries - 1) {
        // エクスポネンシャルバックオフ: 1秒、2秒、4秒
        const delay = Math.pow(2, i) * 1000
        await new Promise(resolve => setTimeout(resolve, delay))
      }
    }
  }

  throw lastError!
}
```

## 認証＆認可

### JWTトークン検証

```typescript
interface JWTPayload {
  userId: string
  email: string
  role: 'admin' | 'user'
}

export function verifyToken(token: string): JWTPayload {
  try {
    const payload = jwt.verify(token, process.env.JWT_SECRET!) as JWTPayload
    return payload
  } catch (error) {
    throw new ApiError(401, '無効なトークン')
  }
}
```

### ロールベースアクセス制御

```typescript
type Permission = 'read' | 'write' | 'delete' | 'admin'

const rolePermissions: Record<string, Permission[]> = {
  admin: ['read', 'write', 'delete', 'admin'],
  moderator: ['read', 'write', 'delete'],
  user: ['read', 'write']
}

export function hasPermission(user: User, permission: Permission): boolean {
  return rolePermissions[user.role].includes(permission)
}
```

## レート制限

```typescript
class RateLimiter {
  private requests = new Map<string, number[]>()

  async checkLimit(
    identifier: string,
    maxRequests: number,
    windowMs: number
  ): Promise<boolean> {
    const now = Date.now()
    const requests = this.requests.get(identifier) || []

    // ウィンドウ外の古いリクエストを削除
    const recentRequests = requests.filter(time => now - time < windowMs)

    if (recentRequests.length >= maxRequests) {
      return false  // レート制限超過
    }

    recentRequests.push(now)
    this.requests.set(identifier, recentRequests)

    return true
  }
}
```

**覚えておいてください**: バックエンドパターンはスケーラブルで保守しやすいサーバーサイドアプリケーションを可能にします。複雑さのレベルに合ったパターンを選択してください。
