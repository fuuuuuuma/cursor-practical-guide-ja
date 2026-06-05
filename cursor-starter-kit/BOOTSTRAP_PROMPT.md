# ブートストラッププロンプト

下のブロックをコピーして、Cursor の Agent パネル（`⌘I`）に貼り付けてください。
`【】` 内を自分のプロジェクトに合わせて書き換えるだけです。

---

```text
あなたはこのリポジトリの初期セットアップ担当です。以下のファイルを生成してください。
既存ファイルがある場合は上書きせず、差分を提案してから反映してください。

# 前提（書き換えてください）
- 言語/FW: 【例: TypeScript / Next.js 15 (App Router)】
- スタイリング: 【例: Tailwind CSS】
- パッケージ管理: 【例: pnpm】
- テスト: 【例: Vitest + React Testing Library】

# 生成してほしいファイル
1. AGENTS.md … プロジェクトの全体方針（プレーンmarkdown、frontmatter不要）。
   上記スタックと「既存ファイルを読んでから編集」「APIを勝手に発明しない」を明記。
2. .cursor/rules/00-core.mdc … alwaysApply: true。技術スタック宣言と一般原則（200語以内）。
3. .cursor/rules/10-style.mdc … globs で対象拡張子を指定（Auto Attached）。コーディング規約。
4. .cursor/rules/20-architecture.mdc … description 指定（Agent Requested）。ファイル配置/層の規約。
5. .cursorignore … 機密(.env, *.key, credentials.*)とビルド生成物をAIアクセスから遮断。
6. .cursorindexingignore … 巨大生成物(dist/, coverage/ 等)をインデックスから除外。
7. .cursor/mcp.json … MCPサーバーの雛形。APIキーは ${env:VAR} 補間で書き、実キーは絶対に書かない。
8. .cursor/hooks.json … afterFileEdit でフォーマッタ、beforeShellExecution で危険コマンドを監査するフック雛形。

MDC のフロントマターは description / globs / alwaysApply を正しく使い分けること。
ルールは命令形・箇条書きで簡潔に。曖昧語を避けること。
生成後、各ファイルの役割を1行ずつ要約して報告すること。
```

---

## 補足

- このプロンプトは「叩き台」を素早く作るためのものです。生成後は必ず内容をレビューしてください。
- 既存プロジェクトに使う場合、`既存ファイルがある場合は上書きせず差分を提案` の一文が効きます。
- より細かく作り込みたい場合は、チャットで `/create-rule` を使うと対話的にルールを追加できます。
