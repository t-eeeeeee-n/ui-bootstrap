---
description: ブランド系 DESIGN.md を npx getdesign 経由でプロジェクトに取得する
argument-hint: "[ブランド名 例: linear.app, claude, cursor]"
---

ユーザーが指定したブランドの DESIGN.md をこのプロジェクトに取得してください。

## ブランド
$ARGUMENTS

## やること

1. 現在のディレクトリに `DESIGN.md` が既に存在しないか確認
2. 存在する場合: 上書きするかユーザーに確認を取る
3. 以下のコマンドを実行:

```bash
npx getdesign@latest add $ARGUMENTS
```

4. 実行結果として `DESIGN.md` が生成されたか確認
5. 生成された DESIGN.md の先頭 80 行程度を読み、以下を報告:
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
「Step 1 完了。次は `/ui-design-localize` で DESIGN.md を
あなたのプロダクト用に書き換えます。」
