# Cursor 実践・応用ガイド 2026 — Composer 2.5 / マルチエージェント / すぐ使えるプロジェクト設定キット

> Cursor を「もう触ったことはある」人が、次の段階＝実戦投入で差をつけるための応用ノート。
> ダウンロードから、最新の Composer 2.5、SpaceX/xAI による買収の話、他ツール比較、そして
> **このページを Cursor に渡すだけで理想的なプロジェクト構成を自動生成できる "スターターキット"** までを一気にまとめた。
> 「Cursor とは何か」の基礎説明はあえて省略し、実践と応用に振り切っている。

---

## ⚠️ この資料の読み方（確度について）

- **作成日**: 2026-06-05 時点の情報。Cursor は更新が非常に速く（後述のとおり数週間でメジャー番号が動く）、仕様は変わる。最終確認は必ず公式（[cursor.com/docs](https://cursor.com/docs)・[changelog](https://cursor.com/changelog)）で。
- **確度マーク**: ✅ 公式ドキュメント/公式ブログで確認 ／ 🔶 一次情報・複数記事はあるが流動的 ／ ⚠️ 単一ソース・未確認・推測
- 本資料は非公式まとめ。Anysphere / Cursor 公式とは無関係。
- **依頼にあった「Composer 2.6」は、2026-06-05 時点では未リリース**。確認できた最新は **Composer 2.5（2026-05-18）**。本資料は捏造を避け 2.5 を「最新」として扱う。2.6 は[未確認・注意事項](#15-未確認注意事項)に記載。

## TL;DR（3行）

- **モデル**: Cursor 自社の **Composer 2.5**（2026-05-18）が主役。Opus 4.7 / GPT-5.5 級の精度を、**1/10 前後のコストと数倍の速度**で出すと公式は主張（数値は[第2章](#2-composer-25-を使いこなす最新の主力モデル)）。実務家のおすすめは「デフォルトを Composer 2.5 Fast」。
- **構造で差がつく**: Cursor の真価は `.cursor/rules/*.mdc`・`AGENTS.md`・`mcp.json`・`hooks.json` などの**プロジェクト設定**を整えてから出る。本資料の[第6章](#6-本命すぐ使えるプロジェクト設定キット自動生成プロンプト付き)に「貼るだけで一式生成されるプロンプト」とテンプレを同梱。
- **地殻変動**: 2026年4月、**SpaceX（xAI と統合済み）が Cursor を $60B で買う権利 or $10B で提携**という発表（買収はまだ確定していない／[第3章](#3-xの買収--spacex--xai--cursor-で何が起きたのか)）。

## 目次

1. [2026年6月の最新地図 — Cursor 3.7 までに何が起きたか](#1-2026年6月の最新地図--cursor-37-までに何が起きたか)
2. [Composer 2.5 を使いこなす（最新の主力モデル）](#2-composer-25-を使いこなす最新の主力モデル)
3. [「Xの買収」= SpaceX × xAI × Cursor で何が起きたのか](#3-xの買収--spacex--xai--cursor-で何が起きたのか)
4. [他AIエージェント/ツールとの比較](#4-他aiエージェントツールとの比較)
5. [ダウンロード & 初期セットアップ（エディタ + CLI）](#5-ダウンロード--初期セットアップエディタ--cli)
6. [【本命】すぐ使えるプロジェクト設定キット（自動生成プロンプト付き）](#6-本命すぐ使えるプロジェクト設定キット自動生成プロンプト付き)
7. [必須機能の実践的な使い方](#7-必須機能の実践的な使い方)
8. [マルチエージェント実践（最大8並列・worktree・best-of-n）](#8-マルチエージェント実践最大8並列worktreebest-of-n)
9. [Bugbot / Cloud Agents / Skills の実務活用](#9-bugbot--cloud-agents--skills-の実務活用)
10. [あまり知られていない上級テクニック集](#10-あまり知られていない上級テクニック集)
11. [入れておくべき拡張機能・MCP サーバー](#11-入れておくべき拡張機能mcp-サーバー)
12. [効率を上げるワークフロー & プロンプト術](#12-効率を上げるワークフロー--プロンプト術)
13. [キーボードショートカット早見表](#13-キーボードショートカット早見表)
14. [出典](#14-出典)
15. [未確認・注意事項](#15-未確認注意事項)

---

## 1. 2026年6月の最新地図 — Cursor 3.7 までに何が起きたか

「久しぶりに Cursor を触る」と、画面が別物になっていて戸惑う。まず現在地を押さえる。

Cursor は **2.0（2025-10-29）で UI を「ファイル中心」から「エージェント中心」に作り直した**のが大きな転換点。✅([changelog/2-0](https://cursor.com/changelog/2-0)) ここから数字が一気に進み、2026年6月時点の公式 changelog の最新は **v3.7**。✅([changelog](https://cursor.com/changelog))

主なマイルストーン（すべて公式 changelog 由来 ✅）:

| 版 | 時期 | 何が入ったか |
|---|---|---|
| 2.0 | 2025-10-29 | エージェント中心UIへ刷新／**Composer**（自社モデル）登場／**最大8エージェント並列**（git worktree 隔離）／ネイティブブラウザツール／「あるモデルで計画→別モデルで実装」 |
| 2.4 | 2026 初頭 | **Subagents（サブエージェント）**・**Skills**・画像生成を導入／`/migrate-to-skills` |
| 3.5 | 2026-05-20 | **/loop スキル**（繰り返し実行）／**Shared Canvases**（チームで共有）／マルチリポ・ノーリポの自動化 |
| 3.6 | 2026-05-29 | **Auto-review Run Mode**（承認プロンプトを減らし安全に長時間自走。許可リスト化した Shell/MCP/Fetch は即実行、残りは分類サブエージェントが判定） |
| 3.7 | 2026-06-04 | **Canvas Design Mode**（キャンバス上で UI 要素を選択・注釈して編集を指示）／**Context Usage Report**（トークン消費を可視化） |

ポイントは2つ。
- **(1) 旧チュートリアルとの食い違いに注意**: 2.0 で `@` メニュー（@Web / @Recent Changes / @Definitions など）が整理され、**多くを「手で指定」しなくてもエージェントが自分で文脈を集める**方式になった。🔶 旧記事の画面と違っても焦らない。
- **(2) 自走の度合いが上がった**: 3.6 の Auto-review で「いちいち承認」を減らせる。ただし後述の安全設定とセットで使うのが前提。

---

## 2. Composer 2.5 を使いこなす（最新の主力モデル）

### 2-1. Composer とは（実務目線の要点）

Composer は **Cursor 自社のエージェント特化モデル**。「同程度に賢い他モデルより約4倍速く、ほとんどのターンを30秒未満で返す」低レイテンシ志向で、**コードベース全体のセマンティック検索**を前提に訓練されている。✅([cursor.com/blog/2-0](https://cursor.com/blog/2-0))

最新版 **Composer 2.5 は 2026-05-18 リリース**。✅([cursor.com/blog/composer-2-5](https://cursor.com/blog/composer-2-5)) 長時間タスクの継続力・複雑な指示への追従が向上した、と公式は説明する。訓練面では「失敗したツール呼び出しに局所的なヒントを与える textual feedback RL」「Composer 2 比 25倍の合成タスク」「MoE 規模のインフラ（Sharded Muon・dual-mesh HSDP）」の3点を強調。✅

> 🔶 サードパーティの複数レビューは「Composer 2.5 は Moonshot の **Kimi K2.5** チェックポイントを土台にしている」と報じている。ただし **Cursor 公式ブログでこの基盤名を明言しているかは未確認**。話す際は「と報じられている」と添えるのが安全。

### 2-2. ベンチマーク（数値は出典付き／相対表現推奨）

公式公表値（Composer 2.5 側）🔶〜✅：

| ベンチ | Composer 2.5 | 位置づけ |
|---|---|---|
| SWE-Bench Multilingual | **79.8%** | Claude Opus 4.7 / GPT-5.5 と「ほぼ同水準」と公式主張 |
| CursorBench v3.1 | **63.2%** | 同上 |
| Terminal-Bench 2.0 | **69.3%** | Opus と同点と公式主張 |

> ⚠️ 注意: 比較対象（Opus 4.7・GPT-5.5）の具体スコアは記事ごとに数ポイント割れる（計測条件・モデル版差）。**断定するなら「Composer 2.5 は SWE-Bench Multilingual で 79.8%、Opus 4.7 とほぼ同等（出典: Cursor 公式ブログ）」のように Composer 側の数値 + 相対表現が安全**。([artificialanalysis](https://artificialanalysis.ai/articles/cursor-composer-2-5-coding-agent-index))

### 2-3. 価格と「燃費」

トークン単価 ✅([cursor.com/blog/composer-2-5](https://cursor.com/blog/composer-2-5)):

- **標準**: $0.50 / 1M 入力、$2.50 / 1M 出力
- **Fast（既定の高速版・同じ知能）**: $3.00 / 1M 入力、$15.00 / 1M 出力

Opus 比で「入力およそ10倍・出力およそ30倍安い」という評価。🔶 X の実務家からは「**$20 の Pro プランでも数億トークン使える燃費**」という報告も。🔶([X](https://x.com/i/status/2062201012170203519))

### 2-4. 実務家のおすすめ設定（X の一次情報）

🔶 複数のパワーユーザーが共有しているセットアップ:

- **デフォルトモデルを「Composer 2.5 Fast」に**。速度と燃費のバランスが良い。([X](https://x.com/i/status/2062617654071017534))
- **「Always Approve（常に承認）」はオフ**にしておく。自走の暴走を防ぐ。([X](https://x.com/i/status/2062617654071017534))
- **「まず計画を立ててから実行して」と一言入れる**だけで暴走防止＆品質向上。([X](https://x.com/i/status/2057041451326779734)) → 仕組み化したのが [Plan Mode](#7-3-plan-mode暴走を止める最重要モード)。

> 重い仕事（大規模リファクタ、難しいデバッグ）は Composer ではなく Claude Opus 系に切り替える、という使い分けも定番。[第4章](#4-2-cursor-vs-claude-code)参照。

---

## 3. 「Xの買収」= SpaceX × xAI × Cursor で何が起きたのか

依頼にあった「Xの買収」は、**SpaceX（2026年2月に xAI と統合済み）による Cursor 買収案件**を指す。中立に事実だけ整理する。**買収はまだ確定していない**（権利を得た段階）点が最重要。

事実関係（2026-04-21 報道、CNBC / TechCrunch などの一次的報道 ✅報道）:

| 項目 | 内容 |
|---|---|
| 発表 | SpaceX が「**年内に Cursor を $60B で完全買収する権利（オプション）**」または「**共同作業に $10B を支払う**」のいずれかを選べる、と発表 |
| 狙い | Cursor の製品＋開発者への流通 × SpaceX のスパコン **Colossus**（Nvidia H100 約100万枚相当と主張）で「世界最高のコーディングAI」を作る |
| 前段 | xAI が Cursor にモデル訓練用の計算資源を貸し始めていた経緯。直前に Cursor のシニアエンジニア2名が xAI へ移籍 |
| 評価額の推移 | 2025/1 $2.5B → Series D（2025/11）$29.3B → 直近調達協議で $50B 規模 |
| 統合の前提 | Musk は 2026年2月に SpaceX と xAI を統合（自称 $1.25兆ドル評価） |

出典: [CNBC](https://www.cnbc.com/2026/04/21/spacex-says-it-can-buy-cursor-later-this-year-for-60-billion-or-pay-10-billion-for-our-work-together.html) / [TechCrunch](https://techcrunch.com/2026/04/21/spacex-is-working-with-cursor-and-has-an-option-to-buy-the-startup-for-60-billion/) / [SiliconANGLE](https://siliconangle.com/2026/04/22/spacex-partners-cursor-ai-training-floats-potential-60b-acquisition/)

開発者にとって（中立的に）:
- **変わらない（報道時点）**: Cursor は引き続き Claude / GPT などの外部モデルも利用・提供。日々の使い勝手に即時の変化はない。✅報道
- **変わりうる**: 提携が進めば、計算資源の制約（外部モデル提供元への依存）から脱し、**自社 Composer をさらに強化する**方向に進む可能性。⚠️（見通し）
- **言い回しの正確さ**: 「買収された」ではなく「**$60B で買える権利を得た／提携した**」が正しい。IPO 30日後に成立、といった具体条件は煽り系の単一ソースが出どころで⚠️。

---

## 4. 他AIエージェント/ツールとの比較

### 4-1. Cursor 内で選べるモデル（2026年6月）

公式の models ページには OpenAI / Anthropic / Google / xAI / Moonshot / 自社 Composer で **40以上**が並ぶ。✅([cursor.com/docs/models-and-pricing](https://cursor.com/docs/models-and-pricing))

| 提供元 | 主なモデル（2026年6月） |
|---|---|
| Anthropic | **Claude Opus 4.8（最新）**・4.7・4.6・4.5（Opus/Sonnet/Haiku）|
| OpenAI | **GPT-5.5**・GPT-5.3 Codex・GPT-5 系（Mini/Fast 等）|
| Google | **Gemini 3.5 Pro**・3.1 Pro・Gemini 3 Pro/Flash・2.5 Flash |
| xAI | **Grok Build 0.1**・Grok 4.3・Grok 4.20 |
| Moonshot | Kimi K2.5 |
| 自社 | **Composer 2.5（最新）**・2・1.5・1 |

> 多くのモデルは「既定では非表示」で、設定から手動で有効化して使う。🔶 まず Composer 2.5 Fast を主役に、難所だけ Opus 4.8 / GPT-5.5 を呼ぶ運用が現実的。

### 4-2. Cursor vs Claude Code

| 観点 | Cursor | Claude Code |
|---|---|---|
| 形態 | VS Code フォークの**エディタ統合型**（2026年1月に CLI も投入）| **ターミナルネイティブ**のエージェント（IDE/デスクトップ/ブラウザにも展開）|
| 得意 | 対話的なスタイリング、内蔵ブラウザ、Tab 補完、Design Mode、単一ファイル編集の速さ・低コスト | 多ファイル横断の自律リファクタ／複雑タスクの精度／トークン効率 |
| トークン効率 | 多め | 同一タスクで Cursor の約 1/5.5 という独立検証も 🔶 |
| 価格感（10名）| Teams ≈ $400/月 | Premium ≈ $1,250/月（約3倍）🔶 |

出典: [Builder.io](https://www.builder.io/blog/cursor-vs-claude-code) / [SitePoint](https://www.sitepoint.com/claude-code-vs-cursor-developer-benchmark-2026/)
**使い分け**: ビジュアル中心・素早い編集は Cursor、重いアーキテクチャ作業は Claude Code。パワーユーザーは両刀が定番。🔶

### 4-3. Cursor vs Windsurf（旧 Codeium）

2025年7月の「72時間ドラマ」で勢力図が変わった。🔶報道
- OpenAI の約 $30億 買収案が頓挫（Microsoft の IP 条項が障害）
- Google が約 $24億 で「逆アクイハイア」: CEO ら主要人材を DeepMind / Gemini へ
- 数日後、**Devin 開発元 Cognition AI** が残資産（コードベース・ブランド・顧客）を取得

→ 2026年時点、**Windsurf と Devin は同一企業（Cognition）の製品**に。
出典: [TechCrunch](https://techcrunch.com/2025/07/14/cognition-maker-of-the-ai-coding-agent-devin-acquires-windsurf/) / [DeepLearning.AI The Batch](https://www.deeplearning.ai/the-batch/google-cognition-carve-up-windsurf-after-openais-failed-3b-acquisition-bid/)
⚠️ 「Windsurf 2.0 が Devin をネイティブ統合」は2026年の追加報道でソースが薄い。

### 4-4. Cursor vs GitHub Copilot

| 観点 | Cursor | GitHub Copilot |
|---|---|---|
| 形態 | 独立 AI IDE（VS Code フォーク）| 多 IDE 対応の拡張（VS Code/JetBrains/Neovim/Xcode）|
| エージェント | IDE 内リアルタイム、多ファイル横断が強い | Coding Agent は**非同期**（issue 割当 → draft PR 生成）|
| 価格 | $20/月〜 | Pro $20/月（記事により $10/月 言及あり）🔶 |

出典: [DigitalOcean](https://www.digitalocean.com/resources/articles/github-copilot-vs-cursor) / [NxCode](https://www.nxcode.io/resources/news/github-copilot-vs-cursor-2026-which-ai-editor-worth-paying)
⚠️ SWE-bench の比較値（Cursor ≈52% / Copilot ≈56% といった記事横断値）は計測条件不明のため断定回避。

### 4-5. その他（Zed / Cline / Aider / Google Antigravity）

| ツール | 形態 | 2026 の要点 |
|---|---|---|
| Google Antigravity | 専用 IDE / エージェント基盤 | I/O 2026 で Desktop/CLI/SDK/Managed Agents API/Enterprise の5面に再構築 🔶 |
| Cline | OSS 拡張 | 500万+ インストール、GitHub 61K+ star。自律マルチステップが売り 🔶 |
| Zed | Rust 製エディタ | 50ms 未満の低遅延、GPU レンダ、AI 内蔵 🔶 |
| Aider | CLI / OSS | 無料・BYOM（自前 API キー）。Claude API 利用で月 $20〜50 程度 🔶 |

ざっくり分類: **IDE 拡張**（Copilot/Cline/Continue）／**専用 IDE**（Cursor/Windsurf/Zed/Antigravity）／**CLI**（Claude Code/Aider/Gemini CLI）／**クラウド**（Devin/OpenHands/Jules）。([artificialanalysis](https://artificialanalysis.ai/agents/coding))

---

## 5. ダウンロード & 初期セットアップ（エディタ + CLI）

### 5-1. エディタ本体

1. [cursor.com](https://cursor.com) にアクセスし「Download」。Mac / Windows / Linux すべて対応（Mac は Apple Silicon 対応）。✅
2. 初回起動で **VS Code からの一括インポート**（拡張機能・設定・キーバインド・テーマ）を案内される。VS Code 利用者はこれでほぼ移行完了。✅
3. サインイン（GitHub / Google など）→ プランは無料の **Hobby** から開始可。最初の1週間は Pro トライアルが付く。🔶

> プラン早見（2026年）🔶: **Hobby $0** ／ **Pro $20/月**（年払い ≈$16/月）／ **Pro+ $60/月**（クレジット3倍）／ **Ultra $200/月**（20倍・優先アクセス）／ **Teams $40/ユーザー/月** ／ Enterprise 応相談。
> 出典: [aiproductivity](https://aiproductivity.ai/blog/cursor-pricing/) / [cursor.com/pricing](https://cursor.com/pricing)

### 5-2. CLI（ターミナル / CI 用）

Cursor はエディタだけでなく **CLI エージェント**も提供。✅([cursor.com/docs/cli/overview](https://cursor.com/docs/cli/overview))

インストール:
```bash
# macOS / Linux / WSL
curl https://cursor.com/install -fsS | bash

# Windows PowerShell
irm 'https://cursor.com/install?win32=true' | iex
```

使い方:
```bash
agent                                   # 対話セッション開始
agent "refactor the auth module to use JWT tokens"   # 初期プロンプト付き
agent -p "review these changes for security issues" --output-format text  # ヘッドレス(CI向け)
agent ls            # 過去チャット一覧
agent resume        # 直近を再開
```
モードは **Agent（既定・全ツール）/ Plan（`/plan`・設計先行）/ Ask（`/ask`・読み取り専用）**。

> 🔶 起動コマンド名は環境/版で `agent` または `cursor-agent` と表記揺れがある。インストールの一行（`curl https://cursor.com/install -fsS | bash`）が確かな起点。

---

## 6. 【本命】すぐ使えるプロジェクト設定キット（自動生成プロンプト付き）

ここが本資料の核。**「このページを Cursor に渡すと、適切なファイル構造を自動で作ってくれる」**を実現する。
使い方は2通り。**(A) 下のブートストラッププロンプトを Cursor の Agent に貼る**か、**(B) リポジトリ同梱の [`cursor-starter-kit/`](cursor-starter-kit) を手でコピー**するか。どちらでも同じ構成になる。

> ⚠️ 重要な前提（2026年6月）:
> - ルールは **`.cursor/rules/*.mdc`** が現行の正。`.cursorrules`（単一ファイル）は**レガシー＝非推奨**。🔶
> - **Memories は 2.1.x で削除**された。永続メモは Rules / `AGENTS.md` で表現する。⚠️
> - **Notepads の公式ドキュメントは消滅（404）**。新構成には含めない。⚠️
> - `commands` は **Skills へ移行が進行中**（2.4 の `/migrate-to-skills`）。本キットは無難な `rules + AGENTS.md + mcp + hooks` を核にする。

### 6-1. ブートストラッププロンプト（Cursor に貼るだけ）

新規/既存プロジェクトを Cursor で開き、Agent に以下をそのまま貼る。`.cursor/` 一式を生成させる依頼文。**自分の技術スタックに合わせて【】内を書き換える**だけ。

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
```

> このプロンプト自体も [`cursor-starter-kit/BOOTSTRAP_PROMPT.md`](cursor-starter-kit/BOOTSTRAP_PROMPT.md) に同梱。

### 6-2. `.cursor/rules/*.mdc` — 4タイプを使い分ける ✅

Project Rules は **`.cursor/rules/` 配下の `.mdc` ファイル**（`.md` は frontmatter が無視されるので不可）。フロントマターの組み合わせで4タイプに分かれる。✅([cursor.com/docs/rules](https://cursor.com/docs/rules))

| タイプ | フロントマター | 挙動 |
|---|---|---|
| **Always** | `alwaysApply: true` | 毎回必ず含まれる（globs/description は無視）|
| **Auto Attached** | `alwaysApply: false` + `globs` | マッチするファイルが文脈に入ると自動添付 |
| **Agent Requested** | `alwaysApply: false` + `description` | Agent が description を読み、関連時に自分で引き込む |
| **Manual** | 項目なし | チャットで `@ルール名` を打った時だけ |

実用テンプレ3種（公式の書式に準拠）:

(A) 技術スタック宣言＝**Always 型**（全リクエストの土台。200語以内に抑えてトークン税を回避 🔶）
```md
---
alwaysApply: true
---

# Tech Stack
- Language: TypeScript (strict). No `any` unless justified with a comment.
- Framework: Next.js 15 (App Router). Server Components by default.
- Styling: Tailwind CSS. No inline style objects.
- Package manager: pnpm. Never suggest npm or yarn.
- Testing: Vitest + React Testing Library.

# General
- Read existing files before editing. Match the surrounding style.
- Never invent APIs. If unsure, say so and ask.
```

(B) コーディング規約＝**Auto Attached 型**（特定ファイルだけに自動適用）
```md
---
globs: src/**/*.tsx
alwaysApply: false
---

# React Component Rules
- Use named exports only (no default exports).
- One component per file; filename matches the component name.
- Props typed with an explicit `interface`, not inline types.
- No business logic in components — extract to hooks under `src/hooks/`.
- Keep components under 200 lines; split when larger.
```

(C) アーキテクチャ規約＝**Agent Requested 型**（必要時に Agent が判断して引く）
```md
---
description: Project structure and where new files must go
alwaysApply: false
---

# File Organization
- API routes: src/app/api/<resource>/route.ts
- Shared types: src/types/ (one file per domain).
- DB access only inside src/server/db/ — never import the DB client into components.
- New service under src/services/<name>/: validate inputs at the boundary,
  return { code, message } errors (never throw raw strings).
- Before creating a file, check for a similar one and extend it.
```

> 作り方ショートカット: チャットで **`/create-rule`** と打ち、やりたいことを説明すると Agent がルールファイルを生成する。✅

### 6-3. `AGENTS.md` — frontmatter 不要の土台 ✅

プロジェクトルートに置く**プレーン markdown**。`.cursor/rules` の「シンプルな代替」として公式対応。サブディレクトリにも置け、そのフォルダ配下で自動適用（より具体的なものが優先）。✅([cursor.com/docs/rules](https://cursor.com/docs/rules))

```md
# Project: <name>

## Stack
- TypeScript / Next.js 15 (App Router) / Tailwind / pnpm / Vitest

## Conventions
- Read existing files before editing. Match surrounding style.
- Never invent APIs or config keys. If unsure, ask.
- Keep PRs small and focused. One concern per change.

## Commands
- dev: `pnpm dev`  / test: `pnpm test`  / lint: `pnpm lint`

## Do not touch
- Generated files in dist/ and build/. Lock files unless deps change.
```

> CLI 側は `AGENTS.md` と `CLAUDE.md` もルートで読み込む。🔶 他ツールと共存しやすい。

### 6-4. `.cursorignore` と `.cursorindexingignore` ✅

| ファイル | 役割 |
|---|---|
| `.cursorignore` | **AI アクセス全般をブロック**（セマンティック検索・Tab・Agent・Inline Edit・`@`参照）。ただし Terminal と MCP 経由はこの制限をバイパスする点に注意 |
| `.cursorindexingignore` | **インデックス（検索）からのみ除外**。AI からはアクセス可能なまま。巨大生成物を検索から外したい時に |

両方とも **`.gitignore` 構文**（`*` `**` `?` `!`否定 `#`）。✅([cursor.com/docs/context/ignore-files](https://cursor.com/docs/context/ignore-files))

`.cursorignore` 例:
```sh
# secrets
**/.env*
**/credentials.json
**/*.key
**/*.pem
# build artifacts
dist/
build/
*.log
```

`.cursorindexingignore` 例:
```sh
dist/
coverage/
**/*.min.js
public/generated/
```

### 6-5. `.cursor/mcp.json` — MCP サーバー設定 ✅

MCP（Model Context Protocol）で外部ツール（GitHub・ブラウザ・公式ドキュメント等）を Agent に接続する。スコープは **プロジェクト `<root>/.cursor/mcp.json`** と **グローバル `~/.cursor/mcp.json`**。✅([cursor.com/docs/context/mcp](https://cursor.com/docs/context/mcp))

ルートは `mcpServers` オブジェクト。**API キーはハードコードせず `${env:VAR}` 補間で渡す**のが鉄則。

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "${workspaceFolder}"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "${env:GITHUB_TOKEN}" }
    },
    "remote-example": {
      "url": "https://api.example.com/mcp",
      "headers": { "Authorization": "Bearer ${env:MY_TOKEN}" }
    }
  }
}
```

使える変数補間 ✅: `${env:NAME}` / `${userHome}` / `${workspaceFolder}` / `${workspaceFolderBasename}` / `${pathSeparator}`。OAuth のリダイレクト URL は固定 `cursor://anysphere.cursor-mcp/oauth/callback`。

### 6-6. `.cursor/hooks.json` — ライフサイクルフック ✅

エージェントループの各段階で自作スクリプトを差し込み、観測・ブロック・改変ができる。✅([cursor.com/docs/agent/hooks](https://cursor.com/docs/agent/hooks))

```json
{
  "version": 1,
  "hooks": {
    "afterFileEdit": [
      { "command": "./.cursor/hooks/format.sh" }
    ],
    "beforeShellExecution": [
      { "command": "./.cursor/hooks/audit.sh", "timeout": 30, "matcher": "rm|curl|git push" }
    ],
    "beforeSubmitPrompt": [
      { "command": "./.cursor/hooks/scan-secrets.sh", "failClosed": true }
    ]
  }
}
```

- 主なイベント: `sessionStart`/`sessionEnd`、`beforeShellExecution`/`afterShellExecution`、`beforeMCPExecution`/`afterMCPExecution`、`beforeReadFile`/`afterFileEdit`、`beforeSubmitPrompt`、`stop`、`subagentStart`/`subagentStop`
- スクリプトは **stdin で JSON を受け取り、stdout で JSON を返す**。終了コード `0`=成功 / `2`=ブロック / それ以外=fail-open（`failClosed: true` で失敗時もブロック）
- 使える環境変数: `CURSOR_PROJECT_DIR`・`CURSOR_VERSION`・`CURSOR_USER_EMAIL`・`CURSOR_TRANSCRIPT_PATH`
- 用途: 編集後の自動フォーマット、シークレット/PII スキャン、危険コマンドのゲート、計測

### 6-7. 推奨ディレクトリ構造（完成形）

```
project-root/
├── AGENTS.md                      # 全体方針（プレーンmd）
├── .cursorignore                  # AIアクセス全般のブロック
├── .cursorindexingignore          # インデックスのみ除外
└── .cursor/
    ├── mcp.json                   # MCPサーバー
    ├── hooks.json                 # フック
    ├── rules/                     # Project Rules（*.mdc）
    │   ├── 00-core.mdc            #   Always: スタック宣言
    │   ├── 10-style.mdc           #   Auto Attached: コーディング規約
    │   └── 20-architecture.mdc    #   Agent Requested: ファイル配置
    └── commands/                  # 任意: カスタム /コマンド（*.md）
        └── review.md
```
グローバル共通分は `~/.cursor/`（`mcp.json` / `hooks.json` / `rules` 相当）に置く。

---

## 7. 必須機能の実践的な使い方

### 7-1. Cursor Tab（補完）を使い倒す ✅

- **マルチライン編集**: 1行だけでなく複数行・不足 import の追加・関連コードの協調編集まで提案。([cursor.com/docs/tab/overview](https://cursor.com/docs/tab/overview))
- **Cursor Prediction（次の編集位置へジャンプ）**: 補完確定後、もう一度 **Tab** で「次に直しそうな場所」へ飛ぶ。連続 Tab で一連の編集を高速化。
- **クロスファイル予測**: ある変更が別ファイルの修正を要する時、別ファイルの編集も予測（ポータルウィンドウ表示）。
- **部分受け入れ**: 候補を単語ごとに採用＝Mac `⌘ →` / Win・Linux `Ctrl →`。
- **スヌーズ/無効化**: 右下インジケータから一時停止や拡張子別の無効化が可能。

### 7-2. Agent モードの基本動線 ✅

`⌘I` でサイドパネルを開いて指示。Agent は Instructions（ルール）+ Tools（検索/編集/ターミナル/ブラウザ）+ Model を統合して動く。([cursor.com/docs/agent/overview](https://cursor.com/docs/agent/overview))
- **Checkpoints**: 大きな変更前に自動スナップショット。タイムラインから安全に巻き戻せる。
- **Queued Messages**: 作業中に次の指示を Enter でキュー。`⌘Enter` でキューを飛ばして即送信。

### 7-3. Plan Mode（暴走を止める最重要モード）✅

コードを書く前に**実装計画**を作る。質問 → コードベース調査 → 計画作成 → レビュー/編集 → 承認後に実装。
- 入り口: チャットで **`Shift+Tab`**、またはモードピッカー。
- 計画は **`.cursor/plans/`** に保存され、チーム共有・再開に使える。
- 2.0 以降は「**あるモデルで計画 → 別モデルで実装**」「並列エージェントで複数案を比較」も可能。
出典: [cursor.com/docs/agent/planning](https://cursor.com/docs/agent/planning) / [blog/agent-best-practices](https://cursor.com/blog/agent-best-practices)
> X の実務家が言う「まず計画を立てて」を、機能として正式化したのがこれ。難しい/曖昧/多ファイルな作業ほど効く。

### 7-4. `@` シンボル（コンテキスト指定）✅

入力中に `@` でポップアップ。現行 mentions ページに明記されているのは ✅:
- **@Files & Folders**（フォルダ選択後 `/` で深掘り）
- **@Docs**（インデックス済みドキュメント。`@Docs > Add new doc` で URL を追加すると索引化され `@Docs React useState` のように引ける）
- **@Terminals**（ターミナル出力）/ **@Past Chats**（過去会話）/ **@Commit**（差分）/ **@Branch**（main との差分）/ **@Browser**（内蔵ブラウザの内容）

> 🔶 旧来の @Web / @Git / @Recent Changes / @Code などは「機能としては概ね残るが、2.0 で手動指定が減りエージェントの自動収集に寄った」。手で全部タグ付けするより、**自動探索に任せて要所だけ `@` 指定**が今風。

---

## 8. マルチエージェント実践（最大8並列・worktree・best-of-n）

Cursor 2.0 で **最大8エージェントを同時実行**できるようになった。git worktree（またはリモートマシン）で各エージェントを隔離し、メインのチェックアウトを汚さない。✅([changelog/2-0](https://cursor.com/changelog/2-0))

- **Worktrees 設定**: `.cursor/worktrees.json`（`setup-worktree-unix` / `-windows` / `setup-worktree` にシェルコマンド配列 or スクリプトパス）。✅([cursor.com/docs/configuration/worktrees](https://cursor.com/docs/configuration/worktrees))
- **ディスク自動掃除**（3.5+）: `cursor.worktreeCleanupIntervalHours: 6`、`cursor.worktreeMaxCount: 25`（1マシン最大25 worktree）。✅
- **使う場所**: worktree は「Agents Window」でネイティブ利用。エディタウィンドウでは `/worktree` と `/best-of-n` スラッシュコマンド。
- **best-of-N**: 同じプロンプトを複数モデルへ同時投入し最良を選ぶ。例 `/best-of-n sonnet,gpt,composer <task>`。各候補は独立 worktree で隔離。✅

実用のコツ 🔶: 並列化は**互いに独立したタスク**（フロント / バック / インフラ等）に割ると衝突しにくい。([forum](https://forum.cursor.com/t/cursor-2-0-split-tasks-using-parallel-agents-automatically-in-one-chat-how-to-setup-worktree-json/140218))

---

## 9. Bugbot / Cloud Agents / Skills の実務活用

### 9-1. Bugbot（自動コードレビュー）✅

PR の diff を解析し、バグ・セキュリティ・品質問題にインラインでコメント＋修正案を残す。([cursor.com/docs/bugbot](https://cursor.com/docs/bugbot))
- セットアップ: ダッシュボードで GitHub / GitLab を連携 → リポジトリ単位で有効化。GitHub には `Cursor Bugbot` チェックが付く。
- 手動トリガ: PR コメントで `cursor review` または `bugbot run`。
- コメントに「Fix in Cursor / Fix in Web」リンクが付き、そのまま修正へ。
- ルール: チーム/リポジトリ/`.cursor/BUGBOT.md` で挙動を制御。インラインで `@cursor remember <事実>` と書くと学習ルール化。
- 課金: 従量制（具体金額は docs に明示なし＝未確認）。

### 9-2. Cloud Agents（旧 Background Agents）✅

隔離された仮想マシン上で動く自律エージェント。クローン・依存導入・シークレット・ネットワークを備えた「フル開発環境」を持ち、**ローカルがネットに繋がっていなくても並列で走る**。([cursor.com/docs/background-agent](https://cursor.com/docs/background-agent))
- 起動点: Cursor Web（cursor.com/agents）、デスクトップ（"Cloud" 選択）、Slack/GitHub/Linear から `@cursor`、API、iOS/Android。
- 環境: `.cursor/environment.json` で Dockerfile を指定、またはスナップショット保存。
- 課金: 選択モデルの API 価格。初回に上限（spending limit）設定が必要。

### 9-3. Skills と `/loop`（2026 の新機能）✅

- **Agent Skills**: ドメイン固有タスクの手順をパッケージ化した「ポータブルでバージョン管理可能」な仕組み。配置は `.cursor/skills/` または `.agents/skills/`（グローバルは `~/` 配下）。各スキルは `SKILL.md`（YAML frontmatter: `name`/`description` 必須、`paths`・`disable-model-invocation` 任意）。([cursor.com/docs/skills](https://cursor.com/docs/skills))
- **/loop**（v3.5・2026-05-20）: プロンプトをローカルでスケジュール反復。「特定の結果に達するまで」繰り返す。例「デプロイ状況を5分ごとに確認」「テストが通るまでこの機能に取り組む」。
- **/create-skill**（チャットで説明 → 雛形生成）、**/migrate-to-skills**（既存ルール/コマンドを Skills へ変換, 2.4+）。✅🔶

---

## 10. あまり知られていない上級テクニック集

実務で効く、見落とされがちな設定・操作をまとめる（確度マーク付き）。

1. **`.cursorindexingignore` で検索だけ外す** — `node_modules` 級の巨大生成物を「AI からは見えるが検索インデックスからは外す」。検索精度が上がる。✅
2. **`@Docs > Add new doc` で社内/最新ドキュメントを索引化** — ライブラリ公式やデザインシステム URL を登録すると `@Docs <名前>` で常に正確な API を引ける。ハルシネーション対策。✅
3. **Plan を `.cursor/plans/` に残してチーム共有/再開** — 大きめタスクは計画ごと version 管理。✅
4. **best-of-N で「賢いモデル × 速いモデル」を競わせる** — `/best-of-n composer,sonnet,gpt <task>` で当たりを選ぶ。✅
5. **Auto-review Run Mode（3.6）で承認回数を減らす** — 許可リスト化した Shell/MCP/Fetch は即実行、残りは分類サブエージェントが判定。長時間自走と安全のバランス。✅ 併せて「Always Approve はオフ」が実務家の定番。🔶
6. **Hooks で `afterFileEdit` → 自動フォーマット** — 保存のたびに Prettier/Black を強制。レビューが楽に。✅
7. **Hooks で `beforeShellExecution` のシークレット/危険コマンドゲート** — `rm -rf` や `git push --force` を機械的にブロック。✅
8. **`@Branch` で main との差分にエージェントを集中** — 大規模リポでも今の作業に文脈を絞れる。✅
9. **新規チャット開始時に `@Past Chats`** — 過去議論を再利用しつつ、文脈ノイズの蓄積（脱線の原因）をリセット。✅
10. **Always ルールは ~200語以内** — 毎リクエストに載る「トークン税」を避ける。長文規約は Auto Attached / Agent Requested に逃がす。🔶
11. **`.mdc` 内で `@template-file` を参照** — 「新規サービスは `@service-template.ts` を起点に」と書くと一貫性が出る。✅
12. **モデルを役割で固定** — 計画=賢いモデル、実装=Composer Fast、レビュー=別モデル、と分けると質と速度を両取り。🔶
13. **Context Usage Report（3.7）でトークンの内訳を見る** — system prompt / tool 定義 / rules の消費を可視化し、重い rules を削る判断に。🔶
14. **`subagentStart`/`subagentStop` フックでサブエージェントを監視** — 並列実行の挙動を計測・制御。✅
15. **CLI ヘッドレス（`agent -p ... --output-format text`）を CI に組み込む** — PR ごとに自動レビュー/自動修正。✅
16. **`.cursor/worktrees.json` の setup スクリプトで依存を自動準備** — 並列エージェントが即作業開始できる。✅
17. **MCP の `${workspaceFolder}` 補間** — filesystem サーバーにプロジェクトルートを自動で渡し、設定を使い回せる。✅
18. **`AGENTS.md` をサブディレクトリにも置く** — モノレポで「この配下はこのルール」とフォルダ別に効かせる。✅
19. **`/migrate-to-skills` で既存資産を移行** — 散らばった rules / commands を Skills に整理（2.4+）。✅
20. **「まず計画→実行」を一言添える** — Plan Mode を使わない時でも暴走防止に効く即効テク。🔶([X](https://x.com/i/status/2057041451326779734))

> ⚠️ 注意: 「YOLO mode」「Notepads」「Memories」はコミュニティ用語/旧機能。現行版では auto-run/auto-review・Rules・AGENTS.md に役割が移っている。古い記事の手順をそのまま試さないこと。

---

## 11. 入れておくべき拡張機能・MCP サーバー

### 11-1. 拡張機能（Cursor は VS Code フォークなので大半が動く）

実用度の高い定番（言語問わず）🔶:
- **ESLint** / **Prettier** — 静的解析・整形。Hooks の自動フォーマットと相性◎
- **Error Lens** — エラー/警告を行内に inline 表示。AI 修正の確認が速い
- **GitLens** — blame / 履歴。エージェントの変更を追いやすい
- **EditorConfig** — チームでインデント等を統一
- **言語別パック**（Python / Go / Rust / Tailwind CSS IntelliSense など）必要分だけ
- **REST Client / Thunder Client** — API を叩いて挙動確認

> ⚠️ 注意: **Microsoft 製の一部拡張**（C#/.NET、Pylance、Remote 系など）は **ライセンス上 Microsoft 製品でのみ動作可**とされ、VS Code フォークでは制限・不安定なことがある。🔶 代替（OpenVSX 版や OSS 実装）を使うか、用途次第で素の VS Code と併用する。

### 11-2. MCP サーバー（Agent の手足を増やす）

実務で効く接続先 🔶（[第6章5](#6-5-cursormcpjson--mcp-サーバー設定-)の `mcp.json` に追記）:
- **filesystem** — ルート配下のファイル操作を安全に
- **github** — issue / PR / リポジトリ操作
- **playwright / browser** — 実ブラウザで UI を検証（2.0 の内蔵ブラウザと併用）
- **context7** — ライブラリ公式ドキュメントを最新で取得（バージョン差異の事故を防ぐ）
- **postgres / sqlite** など DB 系 — スキーマ理解とクエリ

> 鍵は必ず `${env:VAR}` で渡し、リポジトリに実キーをコミットしない。`.env` は `.gitignore` + `.cursorignore` の両方に入れる。

---

## 12. 効率を上げるワークフロー & プロンプト術

公式の Agent ベストプラクティス由来 ✅([blog/agent-best-practices](https://cursor.com/blog/agent-best-practices)):

- **文脈は手で全部 `@` しない**。「authentication flow を直して」と言えば、エージェントが grep + セマンティック検索で関連ファイルを自分で見つける。要所だけ `@` 指定。
- **大きい仕事は Plan Mode から**（`Shift+Tab`）。計画＝具体的な目標を与える行為。`.cursor/plans/` に残す。
- **永続指示は `.cursor/rules/` に集約**。スタイルガイド全文を貼らず、正典コードを `@template` で参照させる。
- **会話を切り替える/混乱したら新規チャット**。同じ機能の反復は継続、別タスクはリセット。多ターン＋要約を重ねると文脈にノイズが溜まり脱線しやすい。
- **並列モデル比較（best-of-N）で当たりを引く**。worktree が自動管理してくれる。
- **「まず計画→次に実装→最後にテスト」を一文で指示**（暴走防止＋品質、X の定番 🔶）。

すぐ使えるプロンプト雛形:
```text
まず修正方針を3案、トレードオフ付きで出して。私が選んだら実装して。
実装は小さなコミット単位で。テストも一緒に。既存のスタイルに合わせて。
```
```text
このバグを「再現 → 原因特定 → 最小修正 → 回帰テスト追加」の順で。
推測で直さず、まず原因を1文で説明してから手を動かして。
```

---

## 13. キーボードショートカット早見表

Mac の初期値（リマップ可能）✅([cursor.com/docs/configuration/kbd](https://cursor.com/docs/configuration/kbd)):

| 操作 | ショートカット |
|---|---|
| Agent サイドパネル トグル | `⌘I` |
| Agent レイアウト トグル | `⌘E` |
| モードメニュー | `⌘.` |
| モード循環（Plan へ等） | `Shift+Tab` |
| インライン編集（エディタ内）| `⌘K` |
| 選択範囲をチャットへ追加 | `⌘⇧L` |
| 新規チャット | `⌘N` / `⌘R` |
| 補完を受け入れ | `Tab` |
| 補完を単語ごとに受け入れ | `⌘→`（Win/Linux: `Ctrl→`）|
| 補完を拒否 | `Esc` |
| メッセージを強制送信（キュー飛ばし）| `⌘Enter` |
| コマンドパレット | `⌘⇧P` |
| 設定 | `⌘,` |

---

## 14. 出典

公式（一次情報）:
- Cursor Docs トップ: https://cursor.com/docs
- Changelog: https://cursor.com/changelog ／ Cursor 2.0: https://cursor.com/changelog/2-0
- Introducing Cursor 2.0 and Composer: https://cursor.com/blog/2-0
- Introducing Composer 2.5: https://cursor.com/blog/composer-2-5
- Composer（RLの解説）: https://cursor.com/blog/composer
- Models & Pricing: https://cursor.com/docs/models-and-pricing ／ Pricing: https://cursor.com/pricing
- Rules: https://cursor.com/docs/rules ／ Context mentions: https://cursor.com/docs/context/mentions
- Ignore files: https://cursor.com/docs/context/ignore-files ／ MCP: https://cursor.com/docs/context/mcp
- Hooks: https://cursor.com/docs/agent/hooks ／ Skills: https://cursor.com/docs/skills
- Worktrees: https://cursor.com/docs/configuration/worktrees ／ Keybindings: https://cursor.com/docs/configuration/kbd
- Bugbot: https://cursor.com/docs/bugbot ／ Background/Cloud Agents: https://cursor.com/docs/background-agent
- Agent best practices: https://cursor.com/blog/agent-best-practices ／ CLI: https://cursor.com/docs/cli/overview
- Tab: https://cursor.com/docs/tab/overview ／ Agent: https://cursor.com/docs/agent/overview ／ Planning: https://cursor.com/docs/agent/planning

買収報道:
- CNBC: https://www.cnbc.com/2026/04/21/spacex-says-it-can-buy-cursor-later-this-year-for-60-billion-or-pay-10-billion-for-our-work-together.html
- TechCrunch: https://techcrunch.com/2026/04/21/spacex-is-working-with-cursor-and-has-an-option-to-buy-the-startup-for-60-billion/
- SiliconANGLE: https://siliconangle.com/2026/04/22/spacex-partners-cursor-ai-training-floats-potential-60b-acquisition/

比較・解説:
- Builder.io（vs Claude Code）: https://www.builder.io/blog/cursor-vs-claude-code
- SitePoint（vs Claude Code）: https://www.sitepoint.com/claude-code-vs-cursor-developer-benchmark-2026/
- DigitalOcean（vs Copilot）: https://www.digitalocean.com/resources/articles/github-copilot-vs-cursor
- Artificial Analysis（エージェント比較/Composer 2.5）: https://artificialanalysis.ai/agents/coding ／ https://artificialanalysis.ai/articles/cursor-composer-2-5-coding-agent-index
- TechCrunch（Cognition が Windsurf 取得）: https://techcrunch.com/2025/07/14/cognition-maker-of-the-ai-coding-agent-devin-acquires-windsurf/
- DeepLearning.AI The Batch（Windsurf 分割）: https://www.deeplearning.ai/the-batch/google-cognition-carve-up-windsurf-after-openais-failed-3b-acquisition-bid/
- 価格: https://aiproductivity.ai/blog/cursor-pricing/

X（一次情報・温度感）:
- Composer 2.5 まとめ: https://x.com/i/status/2062855561524515004
- おすすめ設定（Fast 既定 / Always Approve オフ）: https://x.com/i/status/2062617654071017534
- 「計画→実行」プロンプト: https://x.com/i/status/2057041451326779734
- コスパ報告: https://x.com/i/status/2062201012170203519
- rules 設定例: https://x.com/i/status/2061076207647752697

---

## 15. 未確認・注意事項

- ⚠️ **「Composer 2.6」は 2026-06-05 時点で未リリース**。4ソースで確認済み：公式ブログ `cursor.com/blog/composer-2-6` は **HTTP 404（ページ自体が存在しない）**、公式 changelog にも記載なし、Web検索でも「未発表」、X でも最新は 2.5。確認できた最新は **Composer 2.5（2026-05-18）**。X では次期大型モデルを待望する声があり一部が "2.6" と呼んでいるが、**公式発表・リリースはされていない**🔶。2.6 の情報が出回っていても、公式 changelog / blog（[cursor.com/changelog](https://cursor.com/changelog)）で確認するまで断定しない。リリースされ次第このガイドを更新する想定。
- 🔶 **Composer 2.5 の基盤が「Kimi K2.5」かどうかは未確認**。サードパーティ複数記事の主張であり、Cursor 公式の明言は取れていない。「と報じられている」と添えること。
- ⚠️ **ベンチマークの比較対象（Opus 4.7 / GPT-5.5）の具体スコアは記事ごとに割れる**。Composer 側の数値（SWE-Bench Multilingual 79.8% など）＋「ほぼ同等」という相対表現が安全。
- ⚠️ **SpaceX による Cursor 買収は未確定**（$60B で買える権利／$10B で提携、のいずれかを選べる段階）。「買収された」と言わない。「IPO 30日後に成立」等の具体条件は煽り系単一ソースが出どころ。
- ⚠️ **Memories は 2.1.x で削除**、**Notepads の公式ドキュメントは消滅（404）**。旧記事の手順をそのまま使わない。永続メモは Rules / AGENTS.md で。
- 🔶 **`.cursorrules`（単一ファイル）の「非推奨」明記は公式一次ソースで直接確認できず**（複数の二次情報で一致）。新規では `.cursor/rules/*.mdc` か `AGENTS.md` を使う前提で問題ない。
- 🔶 **CLI の起動コマンド名（`agent` / `cursor-agent`）と一部 UI 名称は版で揺れる**。インストール一行と公式 docs を起点に最新を確認。
- 🔶 **価格・プラン内容・利用上限は変動が激しい**。課金前に [cursor.com/pricing](https://cursor.com/pricing) で最終確認。
- 🔶 **Microsoft 製拡張のフォーク上での可否**はライセンス・版で変わる。重要拡張は事前に動作確認を。
- ⚠️ **`@` シンボルの旧項目（@Web/@Git/@Recent Changes 等）の「残存/統合/廃止」の正確な扱い**は解説記事ベースで、公式現行リストとズレる。現行版で実際に試して確認するのが安全。

---

> この資料は撮影・学習用の非公式まとめです。最新の正確な仕様は必ず Cursor 公式でご確認ください。
> 誤りや古い情報を見つけた場合は、公式ドキュメントの記載を優先してください。
