---
description: ブランド系 DESIGN.md を npx getdesign 経由で取得する（配置先の自動補正付き、複数ブランド並行運用対応）
argument-hint: "[ブランド名 例: linear.app, claude, stripe]"
---

ユーザーが指定したブランドの DESIGN.md を取得します。

## パス解決

出力先は以下の優先順位で決定：
1. 環境変数 `UIB_DESIGN_DIR` が設定されていればそのディレクトリ
2. 未設定ならカレントディレクトリ（`.`）

## 重要: getdesign の仕様

`npx getdesign@latest add` は **Git リポジトリのルート**に DESIGN.md を配置する仕様です。
`cd` しても getdesign がその判断を上書きするため、**出力後に配置先を検証し、
必要なら UIB_DESIGN_DIR に移動する** ロジックを必ず実行してください。

## ブランド
$ARGUMENTS

## 実行手順

### Step 1: 事前スナップショット

以下を記録:
- `UIB_DESIGN_DIR` の絶対パス
- `UIB_DESIGN_DIR` に `DESIGN.md` が既に存在するか
- Git ルート（`git rev-parse --show-toplevel` で取得、失敗したら `null`）
- Git ルートに `DESIGN.md` が既に存在するか

既存の DESIGN.md がある場合は上書き可否をユーザーに確認する。

### Step 2: getdesign を実行

```bash
cd "${UIB_DESIGN_DIR:-.}" && npx getdesign@latest add $ARGUMENTS
```

### Step 3: 配置先の自動補正（重要）

以下の順で DESIGN.md の実配置場所を探索:

1. **`${UIB_DESIGN_DIR}/DESIGN.md`** が存在するか？
   - Yes → 正常配置。Step 4 へ進む
   - No → 次へ

2. **Git ルート直下の `DESIGN.md`** が存在するか？（Step 1 で既存でなかった場合のみ）
   - Yes → **getdesign が Git ルートに配置した**と判断
   - `${UIB_DESIGN_DIR}/DESIGN.md` に移動:
     ```bash
     mv "$(git rev-parse --show-toplevel)/DESIGN.md" "${UIB_DESIGN_DIR}/DESIGN.md"
     ```
   - ユーザーに「getdesign が Git ルートに配置したため、${UIB_DESIGN_DIR} に移動しました」と報告
   - Step 4 へ進む

3. **カレントディレクトリの親（`..`）** に `DESIGN.md` があるか？
   - Yes → 同様に移動して報告
   - No → 次へ

4. **どこにも DESIGN.md が見当たらない場合**:
   - getdesign の実行ログを確認（`→ ...` のような出力があれば、そこが配置先）
   - それでも特定できなければエラー報告:
     ```
     ❌ DESIGN.md の配置先が特定できません。
     以下を手動で確認してください:
     - プロジェクトのどこかに DESIGN.md が作成されていないか
     - npx getdesign の実行ログに配置先が記載されていないか
     ```

### Step 4: 取得結果の報告

`${UIB_DESIGN_DIR}/DESIGN.md` に正しく配置できたら、内容を確認して以下を報告:

- 保存先の絶対パス
- ファイルの行数
- 主要なセクション一覧
- アクセントカラーの HEX 値
- ダーク/ライトの基調
- **Step 3 で移動が発生した場合はその旨を明記**（透明性のため）

## ブランド未指定の場合

$ARGUMENTS が空の場合、以下の推奨ブランドを提示してユーザーに選んでもらう:

| プロダクトの性格 | 推奨ブランド |
|---|---|
| プロ向け、情報密度高め、ダーク | `linear.app`, `cursor`, `opencode` |
| AI プロダクト、温かみ | `claude` |
| エンタープライズ信頼感、ダッシュボード系 | `stripe` |
| モノクロの上品さ、SaaS 教科書 | `vercel` |
| ミニマル、メール系 | `resend` |
| シネマティック、メディア系 | `elevenlabs`, `runway` |
| 明るく親しみやすい、B2C | `notion`, `airtable` |
| デザイン系、ビジュアル重視 | `figma`, `framer` |

全 55 ブランドのカタログは <https://getdesign.md/> または
<https://github.com/VoltAgent/awesome-design-md/tree/main/design-md> を参照。

## 失敗時のフォールバック

`npx getdesign` 自体が失敗した場合（ネットワークエラー等）、
以下のURLをブラウザで開いて DESIGN.md タブから内容をコピーするよう案内する:

```
https://getdesign.md/<ブランド名>/design-md
```

## 次のステップ

完了後、以下を案内:

「Step 1 完了。次は `/ui-design-localize $ARGUMENTS` で DESIGN.md を
あなたのプロダクト用に書き換えます。」

UIB_DOCS_DIR が未設定でカレントに PRODUCT.md / SCREENS.md がない場合は、
先に `/ui-set-paths` でパス設定することを助言する。
