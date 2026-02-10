# Claude Code Configuration Guide

このディレクトリはClaude Code（CLIツール）用のカスタム設定ファイル群です。

## Claude Code CLIのインストール

```bash
# インストール
npm install -g @anthropic-ai/claude-code

# 起動
cd /path/to/L-bridge
claude
```

公式ドキュメント: https://docs.anthropic.com/claude-code

---

## ディレクトリ構成

```
.claude/
|-- commands/          # カスタムスラッシュコマンド
|-- agents/            # 特定の役割を持つエージェント
|-- contexts/          # 開発モード切替
|-- hooks/             # 自動実行スクリプト
|-- rules/             # コーディングルール
|-- skills/            # 再利用可能なスキル定義
|-- plugins/           # プラグイン設定
|-- scripts/           # フック用補助スクリプト
```

---

## 1. Skills（スキル）

専門知識セット。

| スキル | 説明 |
|--------|------|
| `tdd-workflow` | TDD開発フロー |
| `verification-loop` | 検証ループ |
| `security-review` | セキュリティレビュー |
| `coding-standards` | コーディング規約 |
| `continuous-learning` | 継続学習 |
| `strategic-compact` | 戦略的コンテキスト圧縮 |
| `eval-harness` | 評価ハーネス |


### 使用例
使いたい場所からシンボリックリンクを貼る時
```
ln -s ~/.claude .claude
```

claude内で `/skills` で一覧を確認可能
```
User: /tdd 新しいログイン機能を実装したい

Agent: 
# TDD Session: ログイン機能

## Step 1: インターフェース定義
...

## Step 2: テストを先に書く (RED)
...

## Step 3: テスト実行（失敗を確認）
...
```

```
User: /plan マーケット検索機能を追加したい

Agent:
# Implementation Plan: マーケット検索機能

## 要件の整理
...

## 実装フェーズ
...

**確認待ち**: このプランで進めてよいですか？(yes/no/modify)
```

---

## 2. Agents（エージェント）

特定の役割を持ったAIアシスタント。コマンドから自動的に呼び出されます。

| エージェント | 役割 |
|-------------|------|
| `coach` | コーチング担当（成長重視、質問で導く） |
| `tdd-guide` | TDD専門家（テストを先に書く） |
| `planner` | 計画立案者（実装前に計画） |
| `code-reviewer` | コードレビュー担当 |
| `architect` | アーキテクチャ設計 |
| `security-reviewer` | セキュリティチェック |
| `build-error-resolver` | ビルドエラー解決 |
| `e2e-runner` | E2Eテスト実行 |
| `doc-updater` | ドキュメント更新 |
| `refactor-cleaner` | リファクタリング |

---

## 3. Contexts（コンテキスト）

作業モードを切り替えます。

### coaching（コーチングモード）
- 実装者の成長を最優先
- 答えを与えず質問で導く
- コードは書かない（実装者が書く）

### dev（開発モード）
- コード実装を優先
- 動くものを先に作る
- テストを自動実行

### review（レビューモード）
- コード品質を重視
- セキュリティチェック
- ベストプラクティス確認

### research（調査モード）
- 調査・分析を優先
- コード変更は行わない

---

## 4. Hooks（フック）

特定のアクション時に自動実行されるスクリプト。

### 設定されているフック

| タイミング | 内容 |
|-----------|------|
| `SessionStart` | 前回のコンテキストを復元 |
| `SessionEnd` | セッション状態を保存 |
| `PreToolUse` | ツール使用前のチェック |
| `PostToolUse` | ツール使用後の処理 |
| `PreCompact` | コンテキスト圧縮前に状態保存 |
| `Stop` | レスポンス終了時のチェック |

### 主な自動処理

1. **devサーバーのtmux強制**
   - `npm run dev`はtmux内で実行するよう警告

2. **TypeScriptチェック**
   - `.ts`/`.tsx`ファイル編集後に自動で型チェック

3. **Prettierフォーマット**
   - JS/TSファイル編集後に自動フォーマット

4. **console.log警告**
   - `console.log`が残っていると警告

5. **不要なドキュメント作成ブロック**
   - README.md以外の`.md`ファイル作成をブロック

---

## 5. Rules（ルール）

コーディングルールが定義されています。

| ファイル | 内容 |
|---------|------|
| `coding-style.md` | イミュータビリティ、ファイル構成、エラー処理 |
| `testing.md` | テスト要件（80%カバレッジ） |
| `security.md` | セキュリティルール |
| `git-workflow.md` | Gitワークフロー |
| `performance.md` | パフォーマンス要件 |
| `patterns.md` | 推奨パターン |

---

## 参考リンク

- [Claude Code 公式ドキュメント](https://docs.anthropic.com/claude-code)
- [CLAUDE.md ガイド](https://docs.anthropic.com/claude-code/claude-md)
- [Hooks 設定](https://docs.anthropic.com/claude-code/hooks)
