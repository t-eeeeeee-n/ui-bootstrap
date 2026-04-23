---
description: 生成物に対して機械的な品質チェックを実行する
argument-hint: "[元ブランド名 残留チェック用]"
---

生成された mock/ に対して機械的な品質チェックを実行します。

## 前提の確認

1. `mock/` ディレクトリに複数の HTML が存在すること
2. 未完了の場合: `/ui-mock-rest` を先に実行するよう案内して終了

## チェック 1: 残留ブランド語彙

```bash
grep -rniE "\b$ARGUMENTS\b" mock/ DESIGN.md PRODUCT.md SCREENS.md
```

$ARGUMENTS が未指定の場合、ユーザーに元ブランド名を確認する。

また、そのブランド固有の語彙も追加で grep する:
- linear.app → issue, cycle, triage, roadmap, sub-issue
- claude → artifact, constitutional
- stripe → payment, intent
- など

**判定基準**: ヒット 0 件が理想。残っていれば該当行をユーザーに提示して
修正を促す。

## チェック 2: Tailwind パレット直指定の混入

```bash
grep -rnE "(bg|text|border|ring|from|to|via)-(red|blue|green|yellow|purple|violet|orange|pink|amber|slate|gray|zinc|neutral|stone)-[0-9]+" mock/
```

**判定基準**: ヒット 0 件が必須。あれば tokens.css 経由に置換すべき。
該当行をすべて列挙してユーザーに提示。

## チェック 3: CSS 変数利用量

```bash
grep -rnE "var\(--" mock/ | wc -l
```

**判定基準**: 画面数 × 10 以上を目安。下回っていたら多くのスタイルが
ハードコードされている疑い。

## チェック 4: 共通レイアウトの一貫性

以下を Claude Code で確認:

1. すべての画面で `tokens.css` と `components.css` を読み込んでいるか
2. サイドバー・上部バーの HTML 構造が全画面で一致しているか
   （軽微な差異は OK、構造レベルでの分岐がないか）

## 報告フォーマット

結果を以下の形式でユーザーに提示:

```
## 機械的チェック結果

| チェック項目 | 結果 | 判定 |
|---|---|---|
| 残留ブランド語彙 | N件ヒット | ✅/⚠️ |
| Tailwind直指定 | N件ヒット | ✅/❌ |
| CSS変数使用量 | N箇所 | ✅/⚠️ |
| 共通レイアウト一貫性 | 差異N箇所 | ✅/⚠️ |

### 詳細
[判定が ❌ / ⚠️ の項目について、該当行を列挙]

### 推奨アクション
[修正が必要な場合、具体的なコマンドや修正方針]
```

## 次のステップ

全項目 ✅ なら以下を案内:

「すべてのチェックを通過しました。仕上げとして:

1. README.md を作り「どう作ったか」を記録することを推奨します
2. Git でコミット: `git add . && git commit -m 'UI mock bootstrap 完了'`

お疲れさまでした。」
