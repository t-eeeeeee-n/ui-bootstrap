# UI Bootstrap Workflow (v2)

ブランド系 DESIGN.md をベースに、自分のプロダクト用 UI モックを
Claude Code で高速に立ち上げるための手順書。

このドキュメントが本ワークフローの **唯一のソースオブトゥルース** です。
同リポジトリの SKILL.md、`.claude/commands/*.md` はすべてここを参照します。

**v2 での変更点**: 複数ブランド並行運用（例: 同じプロダクトを Linear 版と
Stripe 版で作り分ける）に対応。パス解決を環境変数ベースに統一。

---

## 対象と前提

- **対象ユーザー**: プロダクトの UI モック（3〜10画面前後）を Claude Code で素早く作りたい開発者
- **想定所要時間**: 30分〜90分（プロダクトの複雑さによる）
- **前提環境**: Node.js、Claude Code、Git
- **成果物**: ブランド風の一貫したデザインで統一された HTML モック群 + それを駆動する DESIGN.md / PRODUCT.md / SCREENS.md

---

## ワークフローの全体像

```
[Step 0] 事前準備 + パス設定（複数ブランド運用の場合）
    ↓
[Step 1] DESIGN.md を入手
    ↓
[Step 2] DESIGN.md を脱ブランド化（自プロダクト化）
    ↓
[Step 3] PRODUCT.md を書く（プロダクト仕様・ダミーデータ正典）
    ↓
[Step 4] SCREENS.md を書く（画面仕様）
    ↓
[Step 5] パイロット1画面を先行生成 → 確認
    ↓
[Step 6] 残り画面を一気に生成
    ↓
[Step 7] 機械的チェック（残留ブランド語彙・ハードコード色）
```

各ステップに "完了条件" を設けています。次のステップに進む前に必ず確認してください。

---

## 複数ブランド並行運用について（v2 新規）

同じプロダクトを異なるブランドで作り分ける場合、以下の構造を推奨します：

```
my-product-mock/
├── docs/                      ← 共通（ブランド非依存）
│   ├── PRODUCT.md
│   └── SCREENS.md
├── linear/
│   ├── DESIGN.md              ← Linear 用にローカライズ済み
│   └── mock/
│       ├── tokens.css
│       ├── components.css
│       └── [各画面].html
└── stripe/
    ├── DESIGN.md              ← Stripe 用にローカライズ済み
    └── mock/
```

### パス設定

ブランドディレクトリ（例: `stripe/`）で Claude Code を起動したら、
最初に以下を実行：

```
/ui-set-paths docs=../docs design=. mock=./mock
```

これで以降のコマンドは：
- `docs/PRODUCT.md` と `docs/SCREENS.md` を `../docs/` から読む
- `DESIGN.md` をカレントの `stripe/DESIGN.md` から読む
- 出力を `stripe/mock/` に書き出す

### 単一ブランド運用（シンプル）

従来通り、プロジェクトルートに DESIGN.md、PRODUCT.md、SCREENS.md、mock/ を
並べる構造で作業する場合、パス設定は不要です。デフォルトがすべて「カレント」
になっています。

---

## Step 0: 事前準備

### やること

1. プロダクトのざっくり仕様を固める
   - プロダクト名
   - 何をするサービスか（1〜2行）
   - 主要なユーザー
   - 作りたい画面の一覧（3〜10画面程度を推奨）
2. 作業用ディレクトリを作る

```bash
mkdir my-product-mock && cd my-product-mock
git init
```

3. **複数ブランド運用する予定がある場合**、ディレクトリ構造を先に決める：

```bash
mkdir -p docs <brand-name>  # 例: mkdir -p docs linear
cd <brand-name>
```

4. Claude Code を起動し、パス設定：

```
/ui-set-paths docs=../docs design=. mock=./mock
```

### 完了条件

- [ ] プロダクト名が決まっている
- [ ] 画面一覧が書き出せている
- [ ] 作業ディレクトリが用意できている
- [ ] 複数ブランド運用の場合、パス設定が完了している

---

## Step 1: DESIGN.md を入手

### ブランドを決める

自プロダクトの性格に近いブランドを選びます。代表例：

| プロダクトの性格 | 推奨ブランド |
|---|---|
| プロ向け、情報密度高め、ダーク | `linear.app`、`cursor`、`opencode` |
| エンタープライズ信頼感、ダッシュボード系 | `stripe` |
| モノクロの上品さ、SaaS 教科書 | `vercel` |
| AI プロダクト、温かみ | `claude` |
| ミニマル、メール系、モノスペース | `resend` |
| シネマティック、メディア系 | `elevenlabs`、`runway` |
| 明るく親しみやすい、B2C | `notion`、`airtable` |
| デザイン系、ビジュアル重視 | `figma`、`framer` |

### コマンド（slash command 経由）

```
/ui-design-fetch <brand-name>
```

または手動で：

```bash
# UIB_DESIGN_DIR が設定されていればその中で実行される
cd "${UIB_DESIGN_DIR:-.}"
npx getdesign@latest add <brand-name>
```

### 完了条件

- [ ] `${UIB_DESIGN_DIR}/DESIGN.md` が存在する
- [ ] ファイルサイズが 500 行以上ある

---

## Step 2: DESIGN.md を脱ブランド化

ダウンロード直後の DESIGN.md はブランド名と固有文脈を含んでいます。
これを **自プロダクト用に書き換える** 重要なステップです。

### コマンド

```
/ui-design-localize <元ブランド名>
```

対話的にプロダクト情報をヒアリングした後、DESIGN.md を書き換えます。

### 完了条件

- [ ] Claude Code から「作業完了、要約は以下」の報告が来た
- [ ] 残留ブランド語彙が許容範囲
- [ ] 冒頭付近に "Product Context" セクションが追加されている
- [ ] 新規コンポーネント定義が追加されている

---

## Step 3: PRODUCT.md を書く

### コマンド

```
/ui-product-write
```

複数ブランド運用の場合、2 ブランド目以降は **既存の PRODUCT.md をそのまま
使い回す** のが通常です。スキップしてよい。

### 完了条件

- [ ] `${UIB_DOCS_DIR}/PRODUCT.md` が保存されている
- [ ] ダミーデータが具体的
- [ ] 画面生成時に迷わない粒度になっている

---

## Step 4: SCREENS.md を書く

### コマンド

新規作成：
```
/ui-screens-write
```

既存に追記（画面を追加する場合）：
```
/ui-screens-write --append
```

複数ブランド運用の場合、2 ブランド目以降は SCREENS.md も共通を使うのが基本。
ただしブランドごとに画面構成を変えたい場合（例: Stripe 版にだけ /pricing を
追加）は、追記モードが便利。

### 完了条件

- [ ] `${UIB_DOCS_DIR}/SCREENS.md` が保存されている
- [ ] 全画面分の記述がある
- [ ] パイロット生成候補の画面が特定できている

---

## Step 5: パイロット1画面を先行生成

最複雑画面を1枚だけ先行生成し、共有 CSS 資産（tokens.css / components.css）を
同時に作らせます。

### コマンド

```
/ui-mock-pilot <画面名>
```

### ブラウザで目視確認

`${UIB_MOCK_DIR}/<画面>.html` を開き、以下を確認：

1. **全体の"らしさ"** — 初見でベースブランドに似た印象を受けるか
2. **色の階層** — 背景 / カード / ボーダー / テキストに明確な階層があるか
3. **アクセントカラーの使い方** — 発散せず、現在状態や CTA だけに絞れているか
4. **コンポーネントの差別化** — 類似要素が適切に弁別できるか
5. **情報密度** — 対象ユーザー層に対して過不足ないか

### 完了条件

- [ ] ブラウザで開いて "DESIGN.md のブランドっぽい" と言えるレベル
- [ ] tokens.css と components.css が存在する
- [ ] CSS 変数が十分使われている（50箇所以上目安）

---

## Step 6: 残り画面を一気に生成

### コマンド

```
/ui-mock-rest
```

### 完了条件

- [ ] 全画面の HTML ファイルが生成されている
- [ ] index.html から全画面に飛べる
- [ ] components.css の追加があった場合、内容が妥当

---

## Step 7: 機械的チェック

### コマンド

```
/ui-mock-check <元ブランド名>
```

### 完了条件

- [ ] 残留ブランド語彙 0 件
- [ ] Tailwind 直指定 0 件
- [ ] CSS 変数利用量が十分

---

## 仕上げ

1. プロジェクトルートに README.md を作り、「どう作ったか」を記録
2. 複数ブランド運用なら compare.html で並列表示を作る
3. `ui-design-reviewer` subagent による意味的レビューを実行
4. Git でコミット：

```bash
git add .
git commit -m "UI mock bootstrap: <ブランド名>ベース, <プロダクト名>用"
```

---

## パス解決リファレンス

v2 で導入された環境変数：

| 環境変数 | 意味 | デフォルト |
|---|---|---|
| `UIB_DOCS_DIR` | PRODUCT.md / SCREENS.md の場所 | `.` |
| `UIB_DESIGN_DIR` | DESIGN.md の場所 | `.` |
| `UIB_MOCK_DIR` | mock/ の出力先 | `mock` |

これらは `/ui-set-paths` で設定し、各 slash command が自動参照します。

### 典型パターン

**単一ブランド（従来通り）**:
```
設定不要、すべてカレント基準
```

**複数ブランド並行（ブランドディレクトリから作業）**:
```
/ui-set-paths docs=../docs design=. mock=./mock
```

**プロジェクトルートから全体を制御**:
```
/ui-set-paths docs=./docs design=./stripe mock=./stripe/mock
```

---

## トラブルシューティング

### パス設定後もファイルが見つからない

環境変数が Claude Code のセッションに反映されているか確認：

```bash
echo "$UIB_DOCS_DIR $UIB_DESIGN_DIR $UIB_MOCK_DIR"
```

値が表示されない場合、`/ui-set-paths` を再実行する。

### ブランドを切り替えたら残留語彙が大量にヒットする

2 ブランド目の DESIGN.md の脱ブランド化が不十分な可能性。
`/ui-design-localize` を該当ブランド名で再実行する。

### 既存の mock/ を上書きしたくない

`/ui-set-paths mock=./mock-v2` のように出力先を変えて新規生成する。
比較しやすくなる。

### PRODUCT.md を共有しているが、ブランドごとに差異が出てしまった

共有の PRODUCT.md を "真実" として、各 mock 側をそれに合わせる。
ブランド固有のデータが本当に必要な場合のみ、PRODUCT.md に
「ブランド別差分」セクションを設ける。

---

## このワークフローの設計思想

1. **3 ファイル分離**: `DESIGN.md`（見た目）/ `PRODUCT.md`（中身）/
   `SCREENS.md`（構造）の役割を明確に分ける
2. **パイロット → 量産の 2 フェーズ**: 最複雑画面で共有資産を固めてから横展開
3. **トークン経由の徹底**: Tailwind の直接パレットを禁じ、CSS 変数経由で書かせる
4. **機械的チェック**: grep で残留とハードコードを検出
5. **ステップ間に人間レビュー**: 完全自動化しない
6. **（v2 新規）ブランドを差し替え可能に**: PRODUCT.md / SCREENS.md を共通化し、
   DESIGN.md 入れ替えだけで同一プロダクトの別世界観版を作れる
