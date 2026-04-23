---
description: UI Bootstrap ワークフロー全体を最初から実行する（複数ブランド並行運用対応）
---

UI Bootstrap ワークフローを Step 0 から実行します。各ステップ完了時に人間の
確認を取りながら進める設計です。

## 最初にユーザーに伝えること

「UI Bootstrap ワークフローを開始します。以下の流れで進めます：

1. 事前準備（プロダクト情報のヒアリング + パス設定）
2. DESIGN.md をブランドから取得
3. DESIGN.md をあなたのプロダクト用に書き換え
4. PRODUCT.md を作成（プロダクト仕様とダミーデータ正典）
5. SCREENS.md を作成（画面仕様）
6. パイロット画面 1 枚を先行生成 + 目視確認
7. 残り画面を一気に生成
8. 機械的チェック

各ステップで確認を挟みます。途中で止めたい場合はいつでもそう伝えてください。

個別にステップを実行したい場合は以下のコマンドも使えます：
- `/ui-set-paths docs=<path> design=<path> mock=<path>` — パス設定（複数ブランド並行運用向け）
- `/ui-design-fetch [ブランド名]`
- `/ui-design-localize [元ブランド名]`
- `/ui-product-write`
- `/ui-screens-write [--append]`
- `/ui-mock-pilot [画面名]`
- `/ui-mock-rest`
- `/ui-mock-check [元ブランド名]`」

## 複数ブランド並行運用の案内

「もしこのプロジェクトで **複数ブランドのモックを並行運用** する予定があれば、
以下のディレクトリ構造を推奨します：

```
project-root/
├── docs/              ← 共通（PRODUCT.md, SCREENS.md）
├── linear/
│   ├── DESIGN.md
│   └── mock/
└── stripe/            ← 2 ブランド目以降
    ├── DESIGN.md
    └── mock/
```

この場合、開始時に以下でパスを設定してください：

```
/ui-set-paths docs=../docs design=. mock=./mock
```

（上記は `stripe/` ディレクトリ内から作業する想定。適宜調整）

単一ブランド運用ならデフォルトのまま進めて OK です。」

## 実行

以下の順に、各 slash command に対応する処理を実行する。
各ステップ完了時に **必ずユーザーの確認を取ってから次に進む**。

### Step 0: パス設定の確認（新規）
- 複数ブランド運用かを確認
- YES なら `/ui-set-paths` でパスを設定
- NO ならデフォルトのまま進む

### Step 1: `/ui-design-fetch` 相当
- 推奨ブランド一覧を提示
- ユーザーが選んだブランドで `npx getdesign@latest add <brand>` を実行
- 完了確認

### Step 2: `/ui-design-localize` 相当
- プロダクト情報をヒアリング
- DESIGN.md を書き換えて保存
- 残留語彙チェック
- 完了確認

### Step 3: `/ui-product-write` 相当
- プロダクト仕様の詳細をヒアリング
- ダミーデータ正典を具体化
- PRODUCT.md を作成（既存があれば使い回しを提案）
- 完了確認

### Step 4: `/ui-screens-write` 相当
- 画面一覧をヒアリング（既存 SCREENS.md がある場合は追記モードを検討）
- 各画面の仕様をヒアリング
- 最複雑画面（パイロット対象）を特定
- SCREENS.md を作成
- 完了確認

### Step 5: `/ui-mock-pilot` 相当
- パイロット画面 + tokens.css + components.css を生成
- ブラウザ目視確認のガイドをユーザーに提示
- **ユーザーからの明示的な OK** を待つ

### Step 6: `/ui-mock-rest` 相当
- 残り画面 + index.html を生成
- 完了確認

### Step 7: `/ui-mock-check` 相当
- 機械的チェック 4 項目を実行
- 結果をテーブル形式で報告
- 不備があれば修正提案

### 仕上げ
- README.md を作成する提案
- Git コミットコマンドの提示
- `ui-design-reviewer` subagent を使った意味的レビューの案内

## 重要な注意

- **フル自動で最後まで走らせない**。各ステップで必ず人間のレビューが入る
- ユーザーが「全部任せる」と言っても、最低限以下は明示的に合意を取る:
  - 複数ブランド運用かどうか（パス設定の要否）
  - 使用ブランド
  - プロダクト名
  - 画面一覧
  - パイロット対象画面
- パイロット画面の目視確認を **スキップさせない**
