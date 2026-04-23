---
description: 入手済み DESIGN.md を自プロダクト用に脱ブランド化（複数ブランド並行運用対応）
argument-hint: "[元ブランド名]"
---

入手済みの DESIGN.md をユーザーのプロダクト用に書き換えます。

## パス解決

- DESIGN.md の場所: `${UIB_DESIGN_DIR:-.}/DESIGN.md`
- PRODUCT.md / SCREENS.md の参照（ある場合）: `${UIB_DOCS_DIR:-.}`

## 前提の確認

1. `${UIB_DESIGN_DIR:-.}/DESIGN.md` が存在することを確認
2. 存在しない場合: `/ui-design-fetch` を先に実行するよう案内して終了

## ユーザーに聞くこと

以下を未確認の場合は質問で確認する:

1. **プロダクト名** — "SpecPilot" のような具体名
2. **プロダクトの一行説明** — 何をするサービスか
3. **対象ユーザー** — 誰が使うか
4. **利用のカデンス** — 毎日 / 週次 / プロジェクト単位、等
5. **自プロダクト固有のコンポーネント** — ベースブランドに無い、自プロダクトで
   必要になるコンポーネントのリスト（例: Phase Bar、AI Message Bubble、
   File Tree 等）。コンポーネント名と用途・状態を 1〜2 行で

もし `${UIB_DOCS_DIR:-.}/PRODUCT.md` が既に存在する場合、そこに記載された
プロダクト情報を参考に質問を省略してよい（ユーザーに確認は取る）。

## 実行

すべてのヒアリングが済んだら、対象ファイル `${UIB_DESIGN_DIR:-.}/DESIGN.md` を
以下の方針で書き換える（内部タスクとして実行）：

````markdown
${UIB_DESIGN_DIR:-.}/DESIGN.md を以下の方針で書き換えて上書き保存してください。

## 保持するもの（変更禁止）
- すべてのデザイントークン（色のHEX値、フォント名、サイズ、間隔、角丸、シャドウ等）
- 視覚的な雰囲気
- コンポーネントのスタイル規約（Button、Input、Card、Table、Badge 等の見た目）

## 書き換えるもの
1. ブランド名 "$ARGUMENTS" をすべて "[ヒアリングしたプロダクト名]" に置換
2. そのブランド特有のプロダクト文脈を削除または自プロダクト用に読み替え

## 追加するもの（自プロダクト固有のコンポーネント定義）
[ヒアリングしたコンポーネント一覧を列挙]

## プロダクトコンテキスト（追加セクション "Product Context" として冒頭付近に挿入）
- [ヒアリングしたプロダクト概要]
- [対象ユーザー]
- [利用カデンス]

作業後、変更の要約を 3〜5 行で報告してください。
````

## 完了後のチェック

書き換え完了後、以下の grep を実行して残留語彙チェック:

```bash
grep -niE "\b$ARGUMENTS\b" "${UIB_DESIGN_DIR:-.}/DESIGN.md"
```

ブランド特有の語彙も grep して、残留があれば該当行をユーザーに提示。
代表例：
- linear.app → issue, cycle, triage, roadmap
- stripe → payment, charge, refund, dispute
- claude → artifact, constitutional
- vercel → deployment, edge function

## 次のステップ

完了後、以下を案内:

「Step 2 完了。次は `/ui-product-write` で PRODUCT.md を書きます。
PRODUCT.md は `${UIB_DOCS_DIR:-.}/PRODUCT.md` に保存されます。
複数ブランドで共有する場合、`${UIB_DOCS_DIR:-.}` を共通の docs ディレクトリに
設定しておくと便利です（`/ui-set-paths` 参照）。」
