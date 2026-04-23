---
description: パイロット画面 1 枚 + 共有 CSS 資産を先行生成する
argument-hint: "[パイロット対象画面のファイル名 省略時は SCREENS.md のマーカーから自動判定]"
---

パイロット画面 1 枚と、残り画面で使い回す `tokens.css` / `components.css` を生成します。

## 前提の確認

1. DESIGN.md / PRODUCT.md / SCREENS.md の 3 点が揃っていること
2. 未完了の場合: 不足ファイルをユーザーに通知して終了

## パイロット対象の特定

1. $ARGUMENTS が指定されていればそれをパイロット画面とする
2. 未指定の場合、SCREENS.md を読んで **【パイロット対象】** マーカーのある
   画面を抽出
3. マーカーも無ければ、SCREENS.md の中で記述が最も長い画面を提案して
   ユーザーに確認

## 実行

以下のプロンプトを Claude Code 自身に対するタスクとして実行：

````markdown
DESIGN.md / PRODUCT.md / SCREENS.md を読み込み、
SCREENS.md の「[パイロット画面名]」に相当する HTML モックを生成してください。
これは残り画面の雛形となる「先行パイロット」です。

## 生成ファイル
- `mock/[画面ファイル名].html` — メイン画面
- `mock/tokens.css` — DESIGN.md の全デザイントークンを CSS 変数として集約
- `mock/components.css` — 共通コンポーネントのスタイル

tokens.css と components.css は以降の画面でも使い回す共有資産です。
再利用可能に設計してください。

## 技術要件
- Tailwind CSS の CDN を使ってよいが、色・サイズ・間隔・角丸・シャドウは
  必ず tokens.css の CSS 変数経由で参照すること
- Tailwind の bg-purple-600 等のパレット直指定は禁止
- アイコンは Lucide の SVG をインラインで
- デスクトップ幅（1440px）想定

## 厳守事項
1. DESIGN.md のトークンのみ使用、発明禁止
2. PRODUCT.md のダミーデータ以外を使わない

## 作業後に報告してほしいこと
1. DESIGN.md → tokens.css のマッピング表（主要なものだけ）
2. 追加した独自コンポーネントの実装方針
3. 迷った / 判断保留した箇所があれば列挙
````

## 生成後にユーザーに案内すること

「ブラウザで `mock/[画面].html` を開いて、以下 5 点を目視確認してください：

1. 全体の"らしさ" — 初見でベースブランドに似た印象を受けるか
2. 色の階層 — 背景 / カード / ボーダー / テキストに明確な階層があるか
3. アクセントカラーの使い方 — 発散せず、現在状態や CTA だけに絞れているか
4. コンポーネントの差別化 — 類似要素が適切に弁別できるか
5. 情報密度 — 対象ユーザー層に対して過不足ないか

違和感がある場合は **具体的に言語化** してください。
例: "Phase Bar の現在フェーズが他と区別できない" のように。
"なんか違う" では直しようがありません。」

以下の機械的チェックも併せて実行:

```bash
grep -rnE "var\(--" mock/ | wc -l
grep -rnE "(bg|text|border|ring)-(red|blue|green|yellow|purple|violet|orange|pink|amber|slate|gray|zinc|neutral|stone)-[0-9]+" mock/
```

1 つ目は 50 以上が理想、2 つ目は 0 件が必須。

## 次のステップ

パイロットが OK なら `/ui-mock-rest` で残り画面を一気に生成します。
