# 利用ガイド

## クイックスタート

### 1. 基本的なセットアップ

```bash
# 1. ~/.claude/ ディレクトリを作成
mkdir -p ~/.claude/{agents,commands,rules,skills}

# 2. ファイルをコピー
cp agents/*.md ~/.claude/agents/
cp commands/*.md ~/.claude/commands/
cp rules/*.md ~/.claude/rules/
cp skills/*.md ~/.claude/skills/

# 3. ユーザーレベルCLAUDE.mdを配置
cp examples/user-CLAUDE.md ~/.claude/CLAUDE.md
```

### 2. プロジェクトへの適用

```bash
# プロジェクトルートにCLAUDE.mdを作成
cp examples/CLAUDE.md ./CLAUDE.md

# 必要に応じて編集
# - プロジェクト固有のルール追加
# - 技術スタックに合わせた調整
```

---

## 基本的な使い方

### 新機能開発フロー

```
1. /plan → 実装計画を作成
2. ユーザー確認 → 計画を承認
3. /tdd → テストファースト開発
4. /code-review → 品質チェック
5. git commit → コミット
```

### バグ修正フロー

```
1. /tdd → バグを再現するテストを作成
2. 修正実装 → テストを通過させる
3. /code-review → 副作用がないか確認
4. git commit
```

### リファクタリングフロー

```
1. /plan → リファクタリング計画
2. 既存テスト確認 → カバレッジ確認
3. 段階的な修正 → テスト維持
4. /refactor-clean → 不要コード削除
5. /code-review → 最終確認
```

---

## コマンド別利用シナリオ

### /plan の活用

**いつ使う？**
- 新機能の実装前
- 複雑なリファクタリング前
- 要件が曖昧な場合

**入力例**:
```
/plan 通知機能を追加したい。ユーザーがマーケット解決時に
メールとアプリ内通知を受け取れるようにする。
```

### /tdd の活用

**いつ使う？**
- 新しい関数/コンポーネント作成
- バグ修正
- ビジネスロジック実装

**入力例**:
```
/tdd マーケットの流動性スコアを計算する関数が必要
```

### /code-review の活用

**いつ使う？**
- コードを書いた直後
- コミット前
- PRを作成する前

**入力例**:
```
/code-review 今回の変更をレビューして
```

### /build-fix の活用

**いつ使う？**
- TypeScriptエラー発生時
- npm run build 失敗時
- 型エラーが複数ある場合

**入力例**:
```
/build-fix ビルドが失敗している
```

### /e2e の活用

**いつ使う？**
- 重要なユーザーフローのテスト
- リリース前の検証
- UI変更後の確認

**入力例**:
```
/e2e ログインからダッシュボード表示までのフローをテスト
```

---

## トラブルシューティング

### エージェントが見つからない

```bash
# ファイルの存在確認
ls ~/.claude/agents/

# パスが正しいか確認
# 各エージェントファイルに name: フロントマター必須
```

### コマンドが動作しない

```bash
# commands/ 内のファイルを確認
ls ~/.claude/commands/

# description フロントマター必須
```

### フックが実行されない

```bash
# ~/.claude/settings.json を確認
cat ~/.claude/settings.json | jq '.hooks'

# matcher構文を確認
```

---

## ベストプラクティス

### 1. 段階的な導入

```
Week 1: 基本エージェント（planner, tdd-guide）
Week 2: レビュー系（code-reviewer, security-reviewer）
Week 3: 自動化（hooks, MCP）
Week 4: 本格運用
```

### 2. プロジェクト固有のカスタマイズ

```markdown
# プロジェクトCLAUDE.md に追加

## プロジェクト固有ルール

### データベース
- Prismaを使用
- マイグレーションは必ずレビュー

### API
- REST APIはOpenAPI仕様に準拠
- エンドポイントは/api/v1/で始める
```

### 3. チームでの共有

```bash
# リポジトリに含める
project/
├── .claude/
│   ├── CLAUDE.md  # プロジェクト設定
│   └── agents/    # カスタムエージェント
└── ...
```
