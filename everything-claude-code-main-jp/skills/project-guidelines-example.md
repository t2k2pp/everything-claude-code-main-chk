# プロジェクトガイドラインスキル（例）

これはプロジェクト固有のスキルの例です。あなた自身のプロジェクトのテンプレートとして使用してください。

実際の本番アプリケーションに基づいています: [Zenith](https://zenith.chat) - AI駆動の顧客発見プラットフォーム。

---

## 使用タイミング

設計されている特定のプロジェクトで作業する際にこのスキルを参照してください。プロジェクトスキルには以下が含まれます:
- アーキテクチャ概要
- ファイル構造
- コードパターン
- テスト要件
- デプロイワークフロー

---

## アーキテクチャ概要

**技術スタック:**
- **フロントエンド**: Next.js 15（App Router）、TypeScript、React
- **バックエンド**: FastAPI（Python）、Pydanticモデル
- **データベース**: Supabase（PostgreSQL）
- **AI**: Claude API（ツール呼び出しと構造化出力）
- **デプロイ**: Google Cloud Run
- **テスト**: Playwright（E2E）、pytest（バックエンド）、React Testing Library

**サービス:**
```
┌─────────────────────────────────────────────────────────────┐
│                         フロントエンド                        │
│  Next.js 15 + TypeScript + TailwindCSS                     │
│  デプロイ: Vercel / Cloud Run                               │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                         バックエンド                         │
│  FastAPI + Python 3.11 + Pydantic                          │
│  デプロイ: Cloud Run                                        │
└─────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
        ┌──────────┐   ┌──────────┐   ┌──────────┐
        │ Supabase │   │  Claude  │   │  Redis   │
        │ Database │   │   API    │   │  Cache   │
        └──────────┘   └──────────┘   └──────────┘
```

---

## ファイル構造

```
project/
├── frontend/
│   └── src/
│       ├── app/              # Next.js app routerページ
│       │   ├── api/          # APIルート
│       │   ├── (auth)/       # 認証保護ルート
│       │   └── workspace/    # メインアプリワークスペース
│       ├── components/       # Reactコンポーネント
│       │   ├── ui/           # 基本UIコンポーネント
│       │   ├── forms/        # フォームコンポーネント
│       │   └── layouts/      # レイアウトコンポーネント
│       ├── hooks/            # カスタムReactフック
│       ├── lib/              # ユーティリティ
│       ├── types/            # TypeScript定義
│       └── config/           # 設定
│
├── backend/
│   ├── routers/              # FastAPIルートハンドラー
│   ├── models.py             # Pydanticモデル
│   ├── main.py               # FastAPIアプリエントリ
│   ├── auth_system.py        # 認証
│   ├── database.py           # データベース操作
│   ├── services/             # ビジネスロジック
│   └── tests/                # pytestテスト
│
├── deploy/                   # デプロイ設定
├── docs/                     # ドキュメント
└── scripts/                  # ユーティリティスクリプト
```

---

## コードパターン

### APIレスポンス形式（FastAPI）

```python
from pydantic import BaseModel
from typing import Generic, TypeVar, Optional

T = TypeVar('T')

class ApiResponse(BaseModel, Generic[T]):
    success: bool
    data: Optional[T] = None
    error: Optional[str] = None

    @classmethod
    def ok(cls, data: T) -> "ApiResponse[T]":
        return cls(success=True, data=data)

    @classmethod
    def fail(cls, error: str) -> "ApiResponse[T]":
        return cls(success=False, error=error)
```

### フロントエンドAPI呼び出し（TypeScript）

```typescript
interface ApiResponse<T> {
  success: boolean
  data?: T
  error?: string
}

async function fetchApi<T>(
  endpoint: string,
  options?: RequestInit
): Promise<ApiResponse<T>> {
  try {
    const response = await fetch(`/api${endpoint}`, {
      ...options,
      headers: {
        'Content-Type': 'application/json',
        ...options?.headers,
      },
    })

    if (!response.ok) {
      return { success: false, error: `HTTP ${response.status}` }
    }

    return await response.json()
  } catch (error) {
    return { success: false, error: String(error) }
  }
}
```

---

## テスト要件

### バックエンド（pytest）

```bash
# すべてのテストを実行
poetry run pytest tests/

# カバレッジ付きで実行
poetry run pytest tests/ --cov=. --cov-report=html

# 特定のテストファイルを実行
poetry run pytest tests/test_auth.py -v
```

### フロントエンド（React Testing Library）

```bash
# テストを実行
npm run test

# カバレッジ付きで実行
npm run test -- --coverage

# E2Eテストを実行
npm run test:e2e
```

---

## デプロイワークフロー

### デプロイ前チェックリスト

- [ ] すべてのテストがローカルで成功
- [ ] `npm run build`が成功（フロントエンド）
- [ ] `poetry run pytest`が成功（バックエンド）
- [ ] ハードコードされたシークレットなし
- [ ] 環境変数がドキュメント化
- [ ] データベースマイグレーション準備完了

### デプロイコマンド

```bash
# フロントエンドをビルドしてデプロイ
cd frontend && npm run build
gcloud run deploy frontend --source .

# バックエンドをビルドしてデプロイ
cd backend
gcloud run deploy backend --source .
```

---

## 重要なルール

1. **絵文字なし** コード、コメント、ドキュメントで
2. **イミュータビリティ** - オブジェクトや配列を絶対にミューテートしない
3. **TDD** - 実装前にテストを書く
4. **80%カバレッジ** 最小
5. **多数の小さなファイル** - 200-400行が典型、最大800行
6. **console.logなし** 本番コードで
7. **適切なエラーハンドリング** try/catchで
8. **入力バリデーション** Pydantic/Zodで

---

## 関連スキル

- `coding-standards.md` - 一般的なコーディングベストプラクティス
- `backend-patterns.md` - APIとデータベースパターン
- `frontend-patterns.md` - ReactとNext.jsパターン
- `tdd-workflow/` - テスト駆動開発方法論
