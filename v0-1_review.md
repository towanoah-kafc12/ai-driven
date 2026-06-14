# old/v0.1/main レビュー

## 確認状況

`old/v0.1/main` ブランチを確認できました。

- 確認ブランチ: `old/v0.1/main`
- 最新コミット: `3887b57 ClaudeCodeに対応`
- 直近履歴:
  - `3887b57 ClaudeCodeに対応`
  - `10b5cee LineTodoBotの開発FB反映`
  - `eb5c701 ver.1.0.0`
  - `1542169 first commit`
- 現行 `main` との差分規模: 46 files changed, 3108 insertions(+), 210 deletions(-)

## old/v0.1/main の全体像

`old/v0.1/main` は、特定アプリの実装リポジトリというより、AI駆動開発用のプロジェクトテンプレートです。

中心にある考え方は次の通りです。

- `README.MD` を入口にする
- `AGENTS.md` と `CLAUDE.md` でエージェント別の作業ルールを定義する
- `docs/` にプロジェクト事実、設計、技術選定、完了条件、作業単位を置く
- `docs/tracking/` に不具合と未確定事項を分けて記録する
- `skills/` に繰り返し使う定型手順を分離する
- `scripts/` に lint/test/build/deploy などの実行入口を置く

## 主なファイル構成

```text
AGENTS.md
CLAUDE.md
IDEA.MD
MEMO.MD
README.MD
app/.gitkeep
infra/.gitkeep
scripts/
  bootstrap.sh
  deploy.sh
  lint.sh
  project-build.sh
  test.sh
docs/
  PROJECT.md
  ARCHITECTURE.md
  BOOTSTRAP.md
  DELIVERY.md
  TASKS.md
  TECH-STACK.md
  SKILLS.md
  tracking/
    README.md
    common/{BUGS.md,QUESTIONS.md}
    app/{BUGS.md,QUESTIONS.md}
    infra/{BUGS.md,QUESTIONS.md}
skills/
  INDEX.md
  _shared/README.md
  app-feature/SKILL.md
  aws-infra-change/SKILL.md
  incident-investigation/SKILL.md
  project-bootstrap/SKILL.md
  skill-creator/
```

## 評価できる点

### 1. AIエージェントの作業入口が明確

`README.MD`、`AGENTS.md`、`CLAUDE.md` に読み順と作業ルールがあり、AIがどこから読み始めるべきか迷いにくい構成です。

特に `CLAUDE.md` では、`docs/TASKS.md` の work package 1つを標準の作業単位にしており、Claude Code の Todo 管理や検証フローと相性が良いです。

### 2. docs の責務分離がかなり良い

`docs/` は用途ごとに分かれています。

- `PROJECT.md`: Goal、Non-Goals、Scope、Users、Success Criteria
- `ARCHITECTURE.md`: System Context、Design Principles、Components、Change Management
- `BOOTSTRAP.md`: 初期定義、チェックリスト、早期に決めること
- `DELIVERY.md`: Done Definition、停止条件、Git checkpoint、tracking rules
- `TASKS.md`: work package、実行順、入力、成果物、検証、完了条件
- `TECH-STACK.md`: 技術選定、検証入口、未確定事項
- `SKILLS.md`: skill 化ルールと運用

「人間向けメモ」と「AIが実行に使う仕様」が混ざりにくい構造になっています。

### 3. 未確定事項と不具合を分けている

`docs/tracking/{common,app,infra}/BUGS.md` と `QUESTIONS.md` が分かれており、問題と質問が混ざりにくいです。

これはセッションをまたぐAI駆動開発ではかなり重要です。未解決の質問、実装上の不具合、設計判断の保留を同じTODOに入れると、後続エージェントが判断を誤りやすいためです。

### 4. work package 単位の運用が強い

`docs/TASKS.md` は単なるTODOリストではなく、次のような実行単位を意識しています。

- Phase
- Status
- Inputs
- Outputs
- Validation
- Done condition

これにより、AIが「何を読んで、何を変更し、どう検証したら完了か」を追いやすくなっています。

### 5. skills 化の設計が先回りされている

`skills/INDEX.md`、`docs/SKILLS.md`、各 `skills/*/SKILL.md` によって、定型作業を skill として切り出す方針があります。

既に次のような skill が用意されています。

- `project-bootstrap`
- `app-feature`
- `aws-infra-change`
- `incident-investigation`
- `skill-creator`

特に `skill-creator` は、独自 skill の作成・検証・OpenAI YAML 生成まで含んでおり、テンプレートを育てていく意図が強く見えます。

## 気になる点・弱点

### 1. 初期状態では TBD が多い

`docs/PROJECT.md`、`docs/ARCHITECTURE.md`、`docs/TECH-STACK.md` には `TBD` が多く、テンプレートとしては自然ですが、実プロジェクトに適用した直後は情報不足になりやすいです。

対策としては、`project-bootstrap` の完了条件を厳しめにして、最低限の Goal / Scope / Tech候補 / Stop condition を埋めるまで実装に進まない運用が必要です。

### 2. ドキュメント量が多く、軽量プロジェクトには重い

`docs/`、`tracking/`、`skills/`、`scripts/` が最初から揃っているため、小さいPoCでは運用コストが高く感じられる可能性があります。

ただし、長期運用や複数AIエージェント利用を前提にするなら、この重さは妥当です。

### 3. `README.MD` など拡張子の大文字が目立つ

`README.MD`、`IDEA.MD`、`MEMO.MD` のように `.MD` が大文字です。GitHub上では大きな問題になりにくいですが、一般的な慣習は `.md` なので、他ツールやスクリプトとの連携では小文字に寄せたほうが安全です。

### 4. AGENTS.md と CLAUDE.md の同期コストがある

`AGENTS.md` と `CLAUDE.md` の両方に読み順・作業ルール・検証・tracking・skills が書かれています。

複数ツール対応としては良い一方で、片方だけ更新されると運用品質がぶれる可能性があります。共通ルールは `docs/` に集約し、ツール固有ファイルは差分だけ持つ方針を徹底したほうが良いです。

### 5. scripts は入口だけで、中身の保証は薄い

`scripts/lint.sh`、`scripts/test.sh`、`scripts/project-build.sh` などの入口はありますが、テンプレート段階では実プロジェクト固有の検証内容が未確定です。

`TECH-STACK.md` の Validation Mapping と scripts の実装をセットで更新する運用が必要です。

## 現行 main との差分メモ

現行 `main` から見ると、`old/v0.1/main` には次のような追加・変更が入っています。

- 追加: `IDEA.MD`、`MEMO.MD`、`README.MD`
- 追加: `docs/PROJECT.md`、`docs/ARCHITECTURE.md`、`docs/BOOTSTRAP.md`、`docs/DELIVERY.md`、`docs/TASKS.md`、`docs/TECH-STACK.md`、`docs/SKILLS.md`
- 追加: `docs/tracking/**`
- 追加: `scripts/**`
- 追加: `skills/**`
- 変更: `AGENTS.md`、`CLAUDE.md`
- 削除扱い: `docs/01_pending/template_pending.md`、`docs/02_decision/template_decision.md`、`docs/03_review/template_review.md`
- 削除扱い: `MEMO.md`
- 削除扱い: `v0-1_review.md`

つまり old 側は、初期の pending/decision/review テンプレートから、より実行可能な project docs / tracking / skills 型へ進化した構成です。

## 総評

`old/v0.1/main` は、AI駆動開発を継続運用するためのテンプレートとしてかなり筋が良いです。

特に強いのは、次の3点です。

1. 作業単位を `docs/TASKS.md` の work package に落としていること
2. 未確定事項と不具合を `docs/tracking/` で分離していること
3. 繰り返し作業を `skills/` に逃がす設計があること

一方で、テンプレートとしてはやや重めです。小規模プロジェクトにも使うなら、最小運用セットを次のように定義すると扱いやすくなります。

- 必須: `README.md`、`AGENTS.md`、`CLAUDE.md`、`docs/PROJECT.md`、`docs/TASKS.md`、`docs/DELIVERY.md`、`docs/tracking/common/QUESTIONS.md`
- 準必須: `docs/ARCHITECTURE.md`、`docs/TECH-STACK.md`
- 必要になったら追加: `skills/`、`infra/`、領域別 tracking

この方針にすると、old の良さを残しつつ、初期導入の重さを抑えられます。
