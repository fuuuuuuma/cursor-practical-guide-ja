# Project: <あなたのプロジェクト名>

このファイルはプロジェクト全体の方針を Cursor（および対応エージェント）に伝えるための
プレーン markdown です。frontmatter は不要。サブディレクトリにも置けます（その配下で優先適用）。

## Stack（書き換えてください）
- Language: TypeScript (strict)
- Framework: Next.js 15 (App Router)
- Styling: Tailwind CSS
- Package manager: pnpm
- Testing: Vitest + React Testing Library

## Conventions
- 編集前に既存ファイルを読み、周囲のスタイルに合わせる
- API やコンフィグのキーを勝手に発明しない。不明なら手を止めて聞く
- 変更は小さく、1つの関心事に絞る
- 推測でバグを直さない。まず原因を1文で説明してから直す

## Commands
- dev: `pnpm dev`
- test: `pnpm test`
- lint: `pnpm lint`
- build: `pnpm build`

## Do not touch
- `dist/` `build/` などの生成物
- 依存変更が無い限り lock ファイル
- `.env*` などの機密
