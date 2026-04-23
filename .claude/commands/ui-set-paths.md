---
description: このセッションでの入力・出力パスを設定する（複数ブランド並行運用向け）
argument-hint: "[docs=<path>] [design=<path>] [mock=<path>]"
---

このセッションでの UI Bootstrap ワークフローの入出力パスを設定します。
環境変数として export し、以降のすべての /ui-* コマンドが自動参照します。

## 引数

$ARGUMENTS には以下の形式で指定します（順序は任意、省略可）：

- `docs=<path>` — PRODUCT.md / SCREENS.md の場所（デフォルト: カレント `.`）
- `design=<path>` — DESIGN.md の場所（デフォルト: カレント `.`）
- `mock=<path>` — mock/ の出力先（デフォルト: `mock`）

## 使用例

### 単一ブランド運用（デフォルト挙動）
引数なしで呼び出すと、すべてカレントディレクトリベースに戻ります：
```
/ui-set-paths
```

### 複数ブランド並行運用
例: プロジェクトルートに `docs/` があり、`stripe/` 配下で作業する場合：
```
/ui-set-paths docs=../docs design=. mock=./mock
```

例: 全ディレクトリをルートから指定する場合：
```
/ui-set-paths docs=./docs design=./stripe mock=./stripe/mock
```

## 実行

1. 引数をパースし、`docs=`, `design=`, `mock=` を抽出
2. 環境変数に export：
   ```bash
   export UIB_DOCS_DIR="<docs の値、デフォルトは '.' >"
   export UIB_DESIGN_DIR="<design の値、デフォルトは '.' >"
   export UIB_MOCK_DIR="<mock の値、デフォルトは 'mock' >"
   ```
3. パスが実在するか軽く確認し、存在しない場合は警告を出す
   （ただし mock ディレクトリは未作成でも OK、後で作られる）
4. 設定内容をユーザーに表示：
   ```
   ✓ UIB_DOCS_DIR=<value>
   ✓ UIB_DESIGN_DIR=<value>
   ✓ UIB_MOCK_DIR=<value>
   ```

## 設定後の確認

設定後、以下を案内する：

「パスを設定しました。以降のワークフローはこのパスを使います。
次のステップに進めます。例:
- `/ui-design-fetch stripe`
- `/ui-design-localize stripe`
- ...

パスをリセットしたい場合は引数なしで `/ui-set-paths` を実行してください。」
