---
name: ui-bootstrap
description: Bootstrap a UI mock project by applying a brand-inspired DESIGN.md (obtained via `npx getdesign@latest add <brand>`) to a user's product spec. Use when the user wants to scaffold HTML mocks for a new product using an existing brand's design language (Linear, Claude, Cursor, Vercel, Notion, Figma, etc.), wants to adapt a downloaded DESIGN.md to their own product, or asks to "apply X brand's design to my product mocks". Triggers include phrases like "DESIGN.md を適用したい", "モックを作りたい", "Linear風のUIで作って", "ブランドのデザインを流用して作りたい", or any mention of `getdesign@latest add`.
---

# UI Bootstrap Skill

ブランド系 DESIGN.md をベースにユーザープロダクト用 UI モック一式を立ち上げるスキル。

作業の正典は同リポジトリの `WORKFLOW.md`。このスキルは WORKFLOW.md を
実行可能な形で駆動するオーケストレータとして振る舞う。

## 何をするスキルか

`npx getdesign@latest add <brand>` で入手した、またはこれから入手する
ブランド系 DESIGN.md を土台に、ユーザーのプロダクト用 HTML モック
(3〜10画面程度) を段階的に生成する。

最終的な成果物：
- プロダクト用に脱ブランド化された `DESIGN.md`
- `PRODUCT.md`（プロダクト仕様・ダミーデータ正典）
- `SCREENS.md`（画面仕様）
- `mock/` ディレクトリ配下の HTML 一式と共有 CSS
- `mock/index.html`（画面間ナビゲーション）
- 生成経緯を記した `README.md`

## 実行の心得

1. **WORKFLOW.md を先に読む**。作業ディレクトリで `WORKFLOW.md` が存在する
   場合は最初にその内容を `view` し、差分が無いか確認する。無ければ
   このスキルに同梱されている手順に従う。
2. **ステップ間で必ず人間の確認を取る**。このワークフローは人間のレビューが
   入ることを前提に設計されている。完全自動で最後まで走らせない。
3. **DESIGN.md を勝手に発明しない**。ブランドから入手した DESIGN.md を起点に、
   ユーザー向けに "書き換える" のが本スキルの役割。ゼロから色やタイポを
   作り出さない。
4. **Tailwind の直接パレット禁止**。`bg-purple-600` のようなクラス直指定は
   許さず、常に CSS 変数経由で記述させる。

## ステップ概要

以下は概要。詳細・プロンプト本文・チェックリストはすべて WORKFLOW.md に記載。

### Step 0: 事前準備
ユーザーに以下を確認：
- プロダクト名
- サービスの要旨（1〜2行）
- 対象ユーザー
- 作りたい画面の一覧

### Step 1: DESIGN.md を入手
ユーザーにブランド選定を促す。既に DESIGN.md が存在する場合はスキップ。
コマンドは：
```bash
npx getdesign@latest add <brand-name>
```
代表的ブランド: linear.app, claude, cursor, resend, elevenlabs, notion, airtable, figma, framer

### Step 2: DESIGN.md を脱ブランド化
WORKFLOW.md の "Step 2" に記載のプロンプトを使い、Claude Code 側で
DESIGN.md を書き換えて上書き保存する。書き換え後は残留ブランド語彙の
grep チェックを走らせて報告する。

### Step 3: PRODUCT.md を書く
WORKFLOW.md の "Step 3" のテンプレートを使い、ユーザーから必要情報を
聞き出しながら PRODUCT.md を作成する。ダミーデータ正典の具体性を特に
重視する（"ユーザーA" ではなく具体的な氏名・ID にする）。

### Step 4: SCREENS.md を書く
WORKFLOW.md の "Step 4" のテンプレートを使い、画面ごとの仕様を
SCREENS.md に落とす。**最複雑画面を特定** し、Step 5 のパイロット対象として
マークする。

### Step 5: パイロット画面を先行生成
最複雑画面 1 枚 + tokens.css + components.css を生成。ユーザーに
ブラウザで開いて確認してもらう。WORKFLOW.md の目視確認リスト5項目を
ユーザーに提示し、違和感があれば具体的な言語化を促す。

### Step 6: 残り画面を一気に生成
パイロットが OK なら残り画面 + index.html を生成。tokens.css と
components.css は再利用を徹底させる。

### Step 7: 機械的チェック
WORKFLOW.md の 3 種の grep コマンドを実行し、結果をユーザーに提示。
判定基準に照らして合否を伝える。

## ユーザーとのやり取りの作法

- **日本語で会話している場合は日本語で応答する**
- ステップ間に必ず「完了条件を満たしているか」の確認を挟む
- ユーザーが「任せる」と言っても、ブランド選定・プロダクト名・画面一覧だけは
  必ず明示的に合意を取る。ここが曖昧なまま進むと後段で手戻りが発生する
- 進行中は 1〜2 ステップ先の全体感も軽く共有する
  （「次は DESIGN.md を脱ブランド化、その次に PRODUCT.md を書きます」など）

## このスキルを使わない場合

- ユーザーが単に 1 枚だけの HTML を作りたい場合（オーバーエンジニアリング）
- ブランド系 DESIGN.md を使わず、ゼロからデザインシステムを作りたい場合
  （別スキル: skill-creator などを検討）
- DESIGN.md 自体の作成・改変を目的とする場合（こちらは stitch-skills 等を検討）

## 参照

- WORKFLOW.md（同リポジトリ） — 本スキルの手順の唯一のソースオブトゥルース
- `.claude/commands/` 配下の slash command 群 — 各ステップを個別に実行したい場合の軽量エントリポイント
- https://getdesign.md/ — ブランド DESIGN.md のカタログ
- https://github.com/VoltAgent/awesome-design-md — ブランド DESIGN.md の元リポジトリ
