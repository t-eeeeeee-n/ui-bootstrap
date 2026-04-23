# UI Bootstrap Kit

ブランド系 DESIGN.md をベースに、自分のプロダクト用 UI モックを
Claude Code で高速に立ち上げるための再利用ワークフローキット。

**v2.0.0 より複数ブランド並行運用に対応**（同一プロダクトを Linear 版と Stripe 版で作り分け、等）。
変更内容は [CHANGELOG.md](./CHANGELOG.md) を参照。

## このキットに含まれるもの

```
ui-bootstrap/
├── README.md              ← このファイル
├── CHANGELOG.md           ← バージョン変更履歴
├── WORKFLOW.md            ← 手順の唯一のソースオブトゥルース
├── skill/
│   └── SKILL.md           ← Claude Code Skill（自動発火）
└── .claude/
    └── commands/
        ├── ui-bootstrap.md       ← 全体オーケストレータ
        ├── ui-set-paths.md       ← 入出力パス設定（v2 新規）
        ├── ui-design-fetch.md    ← Step 1: DESIGN.md を入手
        ├── ui-design-localize.md ← Step 2: 脱ブランド化
        ├── ui-product-write.md   ← Step 3: PRODUCT.md 作成
        ├── ui-screens-write.md   ← Step 4: SCREENS.md 作成（--append 対応）
        ├── ui-mock-pilot.md      ← Step 5: パイロット画面生成
        ├── ui-mock-rest.md       ← Step 6: 残り画面量産
        └── ui-mock-check.md      ← Step 7: 機械的チェック
```

## 3つの使い方

用途に応じて以下の 3 通りから選べます。どれも内容的には WORKFLOW.md を
基盤としているため、同じ品質で作業が進みます。

### 使い方 A: 手順書として人間が実行する

一番素朴。`WORKFLOW.md` を開いて上から順にコマンドとプロンプトを
コピペしていく。毎回手順をカスタマイズしたい場合や、初めて使う場合に向く。

```
cd my-new-product
cp /path/to/ui-bootstrap/WORKFLOW.md .
# あとは WORKFLOW.md を開いて順に従う
```

### 使い方 B: Claude Code Skill として自動発火

Claude Code が「UI モックを作りたい」系の発話を検知して自動でこのワークフローを
起動する。何度も使う場合に最も楽。

```
# グローバルに配置する場合
mkdir -p ~/.claude/skills/ui-bootstrap
cp -r /path/to/ui-bootstrap/skill/* ~/.claude/skills/ui-bootstrap/
cp /path/to/ui-bootstrap/WORKFLOW.md ~/.claude/skills/ui-bootstrap/

# プロジェクトローカルに配置する場合
mkdir -p .claude/skills/ui-bootstrap
cp -r /path/to/ui-bootstrap/skill/* .claude/skills/ui-bootstrap/
cp /path/to/ui-bootstrap/WORKFLOW.md .claude/skills/ui-bootstrap/
```

配置後、Claude Code との会話で「Linear風のUIで自分のプロダクトのモック作りたい」
などと言うと自動発火する。

### 使い方 C: Slash Command として明示起動

各ステップを個別に発火したい場合。細かくコントロールしたいワークフローに向く。

```
# プロジェクトローカルに配置
mkdir -p .claude/commands
cp /path/to/ui-bootstrap/.claude/commands/*.md .claude/commands/
cp /path/to/ui-bootstrap/WORKFLOW.md .
```

配置後、Claude Code で以下のように呼び出せる：

* `/ui-bootstrap` — 全工程を順に実行（推奨）
* `/ui-set-paths docs=<path> design=<path> mock=<path>` — 入出力パス設定（複数ブランド運用向け）
* `/ui-design-fetch linear.app` — DESIGN.md を入手
* `/ui-design-localize linear.app` — 脱ブランド化
* `/ui-product-write` — PRODUCT.md を作成
* `/ui-screens-write [--append]` — SCREENS.md を作成（追記モード対応）
* `/ui-mock-pilot session` — パイロット画面生成
* `/ui-mock-rest` — 残り画面量産
* `/ui-mock-check linear.app` — 機械的チェック

### 3 つを併用する場合

B と C は両立可能。A の WORKFLOW.md を手元の参照として置きつつ、
普段は B/C で回すのが実用的。

## 複数ブランド並行運用（v2 新機能）

同一プロダクトを異なるブランドで作り分けたい場合（例: SaaS のモックを
Linear 版と Stripe 版で比較検討したい）、以下のディレクトリ構造を推奨します：

```
my-product-mock/
├── docs/                      ← 共通（ブランド非依存）
│   ├── PRODUCT.md             ← プロダクト仕様・ダミーデータ正典
│   └── SCREENS.md             ← 画面仕様
├── linear/
│   ├── DESIGN.md              ← Linear ベース
│   └── mock/                  ← Linear 版モック
└── stripe/
    ├── DESIGN.md              ← Stripe ベース
    └── mock/                  ← Stripe 版モック
```

ブランドディレクトリ（例: `stripe/`）で Claude Code を起動したら、
最初に `/ui-set-paths` でパスを設定するだけで、以降のコマンドは
共通ドキュメントを `../docs/` から、出力を `./mock/` に自動で振り分けます：

```
/ui-set-paths docs=../docs design=. mock=./mock
/ui-design-fetch stripe
/ui-design-localize stripe
/ui-screens-write --append    # 既存 SCREENS.md に /pricing などを追記
/ui-mock-pilot pricing
/ui-mock-rest
/ui-mock-check stripe
```

詳細は [WORKFLOW.md](./WORKFLOW.md) の「複数ブランド並行運用について」を参照。

単一ブランド運用の場合、パス設定は不要です。すべてカレントディレクトリ基準で
動作する従来通りの挙動になります（**v1 からの後方互換あり**）。

## ワークフローの設計思想

1. **3 ファイル分離**: `DESIGN.md`（見た目）/ `PRODUCT.md`（中身）/
   `SCREENS.md`（構造）の役割を明確に分ける。Claude Code が参照先を迷わない
2. **パイロット → 量産の 2 フェーズ**: 最複雑画面で共有資産を固めてから横展開。
   やり直しコストを最小化
3. **トークン経由の徹底**: Tailwind の直接パレットを禁じ、すべて CSS 変数経由で
   書かせる。DESIGN.md を書き換えるだけで全画面の印象を一気に変えられる状態を作る
4. **機械的チェック**: grep で残留とハードコードを検出。レビューを属人化させない
5. **ステップ間に人間レビュー**: 完全自動化しない。特にパイロット画面は目視確認を必須に
6. **ブランドを差し替え可能に**（v2 新規）: PRODUCT.md / SCREENS.md を共通化し、
   DESIGN.md 入れ替えだけで同一プロダクトの別世界観版を作れる

## Companion: claude-code-agents

このキットで生成したモックのレビューは、
[`claude-code-agents`](https://github.com/t-eeeeeee-n/claude-code-agents) に含まれる
`ui-design-reviewer` subagent と組み合わせると威力を発揮します。

`/ui-mock-check` が担う機械的チェック（残留ブランド語彙、Tailwind直指定の混入、
CSS変数利用量）に対して、`ui-design-reviewer` は以下のような **意味的レビュー** を担当します：

- 画面間の一貫性（サイドバー、タブバー、ボタン使い分け等の統一感）
- DESIGN.md 準拠の視覚的判断（アクセントカラーの発散、surface階層の成立）
- PRODUCT.md のダミーデータ正典との整合性
- "らしさ" の観点（grep では検出できないブランドトーンの乖離）

`claude-code-agents` をグローバルに配置しておけば、UI Bootstrap Kit の
`/ui-mock-rest` で画面を量産した後、自動で `ui-design-reviewer` が発火して
レビュー結果を返してくれるフローになります。

## 参照リンク

* **getdesign.md カタログ**: <https://getdesign.md/>
* **awesome-design-md リポジトリ**: <https://github.com/VoltAgent/awesome-design-md>
* **DESIGN.md 仕様の出所**: Google Stitch (<https://stitch.withgoogle.com/>)

## ライセンス

このキット自体は MIT 相当で自由に使ってください。
取得する DESIGN.md 群は各ブランドの意匠に依拠するため、商用利用時は各自で
トレードマーク・著作権の観点で確認してください。
