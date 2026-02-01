# Example Project CLAUDE.md

This is an example project-level CLAUDE.md file. Place this in your project root.

## Project Overview
やりたいこと
広告経由でWebページ、LINEアプリに遷移後、友達追加まで行ったかどうかを計測し、その成果を広告媒体側に返したい。

解決方法
LIFF（LINE Front-end Framework）をブリッジとして活用し、Webブラウザ側の「広告クリックID」と、LINE側の「ユーザーID（LUID）」をサーバーサイドで100%紐付けする。

​​
ステップ
実行場所
保持データ
アクション
1. 流入
広告LP
AdID (gclid等)
URLパラメータから取得・保持
2. 橋渡し
LIFFアプリ
AdID + LUID
LIFF起動時に両者をサーバーへ送信
3. 成果確定
Webhook
LUID
「追加」イベント受信時にDBを照合



Phase 1：フロントエンド
LP上の追加ボタンをLIFFエンドポイントURLとして、LIFF ブラウザに遷移
遷移時に、URLパラメータの AdID をクエリとして引き継ぐ
Phase 2：LIFFブリッジ
ユーザーがLPのボタンを押すとLIFFが起動。画面には一瞬「読み込み中（またはブランドロゴ）」が表示されるか、あるいは真っ白な状態で処理を行います。
LIFFの onInit 等のライフサイクル内で、liff.getIDToken() から取得したトークン（またはLUID）と、URLパラメータの AdID を自社サーバーへ fetch で送信。
サーバーから 200 OK が返ってきた直後、window.location.replace("https://line.me/ti/p/@...") を実行します。
Note: replace を使うことで、ブラウザの「戻る」ボタンを押した際に計測用LIFFに再度捕まるループを防ぎます。
Phase 3：バックエンド
イベント監視: Messaging APIの follow イベントを受信
マッチング: Webhookに含まれる userId をキーにDBを検索し、紐付く AdID を特定
ポストバック: 各広告媒体（Google Ads API / Meta Conversions API等）へ成果データを送信

技術スタック
Node.js, Fastify, Redis, React(Front), GCP


## Critical Rules

### 1. Code Organization

- Many small files over few large files
- High cohesion, low coupling
- 200-400 lines typical, 800 max per file
- Organize by feature/domain, not by type

### 2. Code Style

- No emojis in code, comments, or documentation
- Immutability always - never mutate objects or arrays
- No console.log in production code
- Proper error handling with try/catch
- Input validation with Zod or similar

### 3. Testing

- TDD: Write tests first
- 80% minimum coverage
- Unit tests for utilities
- Integration tests for APIs
- E2E tests for critical flows

### 4. Security

- No hardcoded secrets
- Environment variables for sensitive data
- Validate all user inputs
- Parameterized queries only
- CSRF protection enabled

## File Structure

```
src/
|-- app/              # Next.js app router
|-- components/       # Reusable UI components
|-- hooks/            # Custom React hooks
|-- lib/              # Utility libraries
|-- types/            # TypeScript definitions
```

## Key Patterns

### API Response Format

```typescript
interface ApiResponse<T> {
  success: boolean
  data?: T
  error?: string
}
```

### Error Handling

```typescript
try {
  const result = await operation()
  return { success: true, data: result }
} catch (error) {
  console.error('Operation failed:', error)
  return { success: false, error: 'User-friendly message' }
}
```

## Environment Variables

```bash
# Required
DATABASE_URL=
API_KEY=

# Optional
DEBUG=false
```

## Available Commands

- `/tdd` - Test-driven development workflow
- `/plan` - Create implementation plan
- `/code-review` - Review code quality
- `/build-fix` - Fix build errors

## Git Workflow

- Conventional commits: `feat:`, `fix:`, `refactor:`, `docs:`, `test:`
- Never commit to main directly
- PRs require review
- All tests must pass before merge
