# Cursor スターターキット（すぐ使えるプロジェクト設定一式）

このフォルダは、Cursor を「整った状態」で使い始めるための設定テンプレート集です。
親フォルダの [`../README.md`](../README.md)（実践・応用ガイド）とセットで使ってください。

## 使い方は2通り

### A. Cursor に自動生成させる（おすすめ）
1. 自分のプロジェクトを Cursor で開く
2. [`BOOTSTRAP_PROMPT.md`](BOOTSTRAP_PROMPT.md) の中身をコピー
3. Agent パネル（`⌘I`）に貼り、`【】` 内を自分のスタックに書き換えて送信
4. 提案された差分を確認して反映

### B. 手でコピーする
このフォルダの中身を、自分のプロジェクトのルートにコピーするだけ。

```
コピー元（このフォルダ）          → コピー先（あなたのプロジェクト root）
AGENTS.md                        → AGENTS.md
.cursorignore                    → .cursorignore
.cursorindexingignore            → .cursorindexingignore
.cursor/mcp.json                 → .cursor/mcp.json
.cursor/hooks.json               → .cursor/hooks.json
.cursor/rules/00-core.mdc        → .cursor/rules/00-core.mdc
.cursor/rules/10-style.mdc       → .cursor/rules/10-style.mdc
.cursor/rules/20-architecture.mdc→ .cursor/rules/20-architecture.mdc
.cursor/commands/review.md       → .cursor/commands/review.md
```

## 中身（ファイル一覧）

| ファイル | 役割 |
|---|---|
| `AGENTS.md` | プロジェクト全体方針（プレーン markdown） |
| `.cursorignore` | AI アクセス全般のブロック（機密・生成物） |
| `.cursorindexingignore` | 検索インデックスからのみ除外 |
| `.cursor/mcp.json` | MCP サーバー設定（鍵は `${env:VAR}` 補間） |
| `.cursor/hooks.json` | フック（自動整形・危険コマンド監査） |
| `.cursor/rules/00-core.mdc` | Always 型: 技術スタック宣言 |
| `.cursor/rules/10-style.mdc` | Auto Attached 型: コーディング規約 |
| `.cursor/rules/20-architecture.mdc` | Agent Requested 型: ファイル配置 |
| `.cursor/commands/review.md` | カスタム `/review` コマンド例 |

## カスタマイズの最初の3手

1. `AGENTS.md` と `.cursor/rules/00-core.mdc` の **技術スタック行を自分のものに**書き換える
2. `.cursorignore` に **自分の機密ファイルのパス**を追加する
3. `.cursor/mcp.json` の **使う MCP サーバーだけ残す**（鍵は環境変数で）

> ⚠️ 仕様は変わります。各ファイルの正確な現行仕様は親ガイドの「出典」にある Cursor 公式ドキュメントで最終確認してください。
> ⚠️ `.cursor/hooks/` のスクリプト本体（format.sh など）は環境依存のため同梱していません。`hooks.json` はコマンドパスの雛形です。
