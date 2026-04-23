---
description: ブランド系 DESIGN.md を npx getdesign 経由で取得する（複数ブランド並行運用対応）
argument-hint: "[ブランド名 例: linear.app, claude, stripe]"
---

ユーザーが指定したブランドの DESIGN.md を取得します。

## パス解決

出力先は以下の優先順位で決定：
1. 環境変数 `UIB_DESIGN_DIR` が設定されていればそのディレクトリ
2. 未設定ならカレントディレクトリ（`.`）

パス未設定の場合、`/ui-set-paths` で事前設定することを推奨する旨を案内する。

## ブランド
$ARGUMENTS

## やること

1. パス解決を実施し、DESIGN.md の保存先を `${UIB_DESIGN_DIR:-.}/DESIGN.md` と決定
2. 保存先に既に DESIGN.md が存在しないか確認
3. 存在する場合: 上書きするかユーザーに確認を取る
4. 以下のコマンドを `${UIB_DESIGN_DIR:-.}` で実行:

```bash
cd "${UIB_DESIGN_DIR:-.}" && npx getdesign@latest add $ARGUMENTS
```

5. 実行結果として DESIGN.md が生成されたか確認
6. 生成された DESIGN.md の先頭 80 行程度を読み、以下を報告:
   - 保存先の絶対パス
   - ファイルの行数
   - 主要なセクション一覧
   - アクセントカラーの HEX 値
   - ダーク/ライトの基調

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

## 失敗時のフォールバック

`npx getdesign` が失敗した場合、以下のURLをブラウザで開いて DESIGN.md タブから
内容をコピーするよう案内する:

```
https://getdesign.md/<ブランド名>/design-md
```

## 次のステップ

完了後、以下を案内:

「Step 1 完了。次は `/ui-design-localize $ARGUMENTS` で DESIGN.md を
あなたのプロダクト用に書き換えます。」

UIB_DOCS_DIR が未設定でカレントに PRODUCT.md / SCREENS.md がない場合は、
先に `/ui-set-paths` でパス設定することを助言する。
