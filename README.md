# Claude Code Configuration Guide

このディレクトリはClaude Code（CLIツール）用のカスタム設定ファイル群です。

## 重要: Cursor vs Claude Code

| 機能 | Cursor | Claude Code (CLI) |
|------|--------|-------------------|
| `CLAUDE.md` | 読み込まれる | 読み込まれる |
| `.claude/commands/` | 動作しない | `/tdd`等のコマンドで呼び出し |
| `.claude/agents/` | 動作しない | コマンドから呼び出し |
| `.claude/hooks/` | 動作しない | 自動実行 |
| `.claude/rules/` | 動作しない | 参照される |
| `.claude/skills/` | 動作しない | 参照される |

**Cursorを使用している場合**: `CLAUDE.md`のみが有効です。`.claude`ディレクトリの機能を使うにはClaude Code CLIをインストールしてください。

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

## 1. Commands（コマンド）

Claude Codeで`/`を入力すると使えるカスタムコマンド。

### 使用可能なコマンド一覧

| コマンド | 説明 |
|----------|------|
| `/coaching` | コーチングモード（成長重視、質問で導く） |
| `/tdd` | テスト駆動開発ワークフロー（テストを先に書く） |
| `/plan` | 実装計画を作成（コードを書く前に） |
| `/code-review` | コードレビューを実行 |
| `/build-fix` | ビルドエラーを修正 |
| `/e2e` | E2Eテストを実行 |
| `/refactor-clean` | リファクタリングとコード整理 |
| `/test-coverage` | テストカバレッジを確認 |
| `/checkpoint` | 作業のチェックポイントを作成 |
| `/verify` | コードの検証 |

### 使用例

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

## 6. Skills（スキル）

再利用可能な専門知識セット。

| スキル | 説明 |
|--------|------|
| `tdd-workflow` | TDD開発フロー |
| `verification-loop` | 検証ループ |
| `security-review` | セキュリティレビュー |
| `coding-standards` | コーディング規約 |
| `continuous-learning` | 継続学習 |
| `strategic-compact` | 戦略的コンテキスト圧縮 |
| `eval-harness` | 評価ハーネス |

---

## 7. クイックスタート

### Claude Code CLIで使う場合

```bash
# 1. プロジェクトディレクトリに移動
cd /path/to/L-bridge

# 2. Claude Codeを起動
claude

# 3. コマンドを使う
/plan ログイン機能を実装したい
```

### Cursorで使う場合

`CLAUDE.md`に書かれたルールのみが適用されます。

- プロジェクト概要
- コーディングルール
- ファイル構成
- エラー処理パターン
- 環境変数

---

## 8. カスタマイズ

### 新しいコマンドを追加

`commands/`に新しい`.md`ファイルを作成:

```markdown
---
description: コマンドの説明
---

# Command Name

## What This Command Does
...

## How It Works
...
```

### 新しいフックを追加

`hooks/hooks.json`を編集:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "tool == \"Edit\"",
        "hooks": [{
          "type": "command",
          "command": "echo 'File edited'"
        }],
        "description": "ファイル編集後の処理"
      }
    ]
  }
}
```

---

## 9. トラブルシューティング

### コマンドが動かない
- Claude Code CLIを使っているか確認
- Cursorでは`.claude/commands/`は動作しません

### フックが実行されない
- `hooks.json`の構文エラーを確認
- スクリプトのパスが正しいか確認

### エージェントが呼び出されない
- `commands/`内のコマンドファイルが正しくエージェントを参照しているか確認

---

## 10. 参考リンク

- [Claude Code 公式ドキュメント](https://docs.anthropic.com/claude-code)
- [CLAUDE.md ガイド](https://docs.anthropic.com/claude-code/claude-md)
- [Hooks 設定](https://docs.anthropic.com/claude-code/hooks)
