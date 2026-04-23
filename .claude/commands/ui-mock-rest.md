---
description: パイロット画面以外の残り全画面を一気に生成する（複数ブランド並行運用対応）
---

パイロット画面で確立した共有 CSS 資産を使い回し、残り画面を量産します。

## パス解決

- SCREENS.md / PRODUCT.md: `${UIB_DOCS_DIR:-.}`
- DESIGN.md: `${UIB_DESIGN_DIR:-.}/DESIGN.md`
- 出力先: `${UIB_MOCK_DIR:-mock}/`

## 前提の確認

1. `${UIB_MOCK_DIR:-mock}/` 配下に少なくとも 1 画面の HTML と
   `tokens.css` / `components.css` が存在すること
2. SCREENS.md の記述と現在の `${UIB_MOCK_DIR:-mock}/` の状態を突合し、
   未生成の画面を列挙
3. 未生成画面がゼロの場合: `/ui-mock-check` に進むよう案内して終了

## ユーザーに最終確認

「以下 [N] 画面を一気に生成します。進めてよいですか？」

- `${UIB_MOCK_DIR:-mock}/[画面1].html`
- `${UIB_MOCK_DIR:-mock}/[画面2].html`
- ...
- `${UIB_MOCK_DIR:-mock}/index.html`（画面ナビゲーション）

「もしパイロット画面にまだ違和感があるなら、先に
`/ui-mock-pilot` で再生成することを推奨します。」

## 実行

以下のプロンプトを Claude Code 自身に対するタスクとして実行：

````markdown
${UIB_MOCK_DIR:-mock}/[パイロット画面].html が期待通り生成された前提で、
${UIB_DOCS_DIR:-.}/SCREENS.md の残り画面を生成してください。

## 参照ファイル
- ${UIB_DESIGN_DIR:-.}/DESIGN.md
- ${UIB_DOCS_DIR:-.}/PRODUCT.md
- ${UIB_DOCS_DIR:-.}/SCREENS.md

## 生成ファイル
[未生成画面のファイル名を列挙、すべて ${UIB_MOCK_DIR:-mock}/ 配下]
- ${UIB_MOCK_DIR:-mock}/index.html — 全画面へのナビゲーション

## 厳守事項
- ${UIB_MOCK_DIR:-mock}/tokens.css と ${UIB_MOCK_DIR:-mock}/components.css を再利用
- 新規コンポーネントが必要になったら、勝手に増やさず components.css に
  追記する形で統合し、「何を足したか / DESIGN.md のどの規約から派生させたか」を
  報告すること
- PRODUCT.md のダミーデータ正典を全画面で使い回す
- 共通レイアウトの構造と見た目は全画面で完全一致させる
- HTML 内の CSS 読み込みは相対パス `<link rel="stylesheet" href="tokens.css">`

## 画面ごとの特に気をつけたい点
[SCREENS.md から各画面の特徴的な要求事項を抽出して記載]

## 作業後に報告してほしいこと
1. 画面ごとに使った主要コンポーネントの一覧
2. tokens.css / components.css に追加があった場合、その内容と理由
3. SCREENS.md の記述と実装で乖離した点があれば列挙
````

## 生成後

ユーザーに以下を案内:

「全画面の生成が完了しました。
`${UIB_MOCK_DIR:-mock}/index.html` をブラウザで開いて全画面に目を通してください。
その後、`/ui-mock-check` で機械的チェックを実行します。」
