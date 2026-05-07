# CLAUDE.md

## Purpose

このファイルは、このリポジトリにおける Claude Code の入口です。
素早く作業を始めるための最小ルールをここに置き、詳細な共通ルールは `AGENTS.md` に委譲します。

## Read Order

最小限の文脈から読み始め、必要になった範囲だけ追加で確認します。

1. `CLAUDE.md`
2. `AGENTS.md`
3. `docs/TASKS.md`
4. 選択した work package の `Inputs`
5. 対象領域に関係する `docs/`、`docs/tracking/`、`skills/`

## Work Selection

- `docs/TASKS.md` の work package 1 つを標準の作業単位にします。
- `In Progress` の work package があれば、新しい作業より先に閉じます。
- `In Progress` がなければ、ユーザーの直接指示がない限り、最高優先度の `Ready` work package を選びます。
- `Blocked` の work package は、ブロッカーが解消するかユーザーが明示的に範囲を変えるまで着手しません。

## Claude Code Workflow

- 非自明な変更、複数ファイル変更、複数の方針があり得る変更では、実装前に計画を立てます。
- 3 手順以上の作業は Todo で進捗を管理します。
- 編集前に、対象範囲と方針を短く伝えます。
- 大きな作り替えより、目的に対して最小の差分を優先します。
- 実装、docs、tracking、validation を active な work package と揃えます。

## Validation

- コードやインフラ変更では、`./scripts/lint.sh` と `./scripts/test.sh` を安定した検証入口として使います。
- 技術スタックがまだ TBD の場合、scripts はテンプレート入口として維持し、将来の実コマンド対応は `docs/TECH-STACK.md` に記録します。
- UI 変更では、アプリを起動し、ブラウザで変更フローを確認してから完了扱いにします。
- 失敗した検証や実行できない検証は、関連 work package または tracking に記録します。

## Stop and Confirm

次の操作は、ユーザーがその操作を明示的に許可していない限り、停止して確認します。

- deploy または `./scripts/deploy.sh` の実行
- push、PR 作成、履歴改変、force push
- ファイル、DB、クラウド、インフラに対する破壊的操作
- 影響範囲が大きい依存関係の追加または削除
- 外部サービスへのデータ送信、共有設定の変更
- 秘密情報、認証情報、IAM、本番リソース、課金、公開範囲に関わる操作

## Tracking Unknowns

- 安全性、完了条件、コスト、本番影響、公開範囲に関わる不明点は推測で埋めません。
- 停止すべき不明点は `docs/tracking/*/QUESTIONS.md` に記録します。
- 実装中に見つけた不具合は `docs/tracking/*/BUGS.md` に記録します。
- 仮説で進めてよい場合は、仮説と見直し条件を work package または tracking に残します。

## Skills

このリポジトリの `skills/` は、Claude Code の slash command ではなく、プロジェクト内の手順ドキュメントです。
PJ 初期ビルド、app、infra、障害調査、skill 保守では `skills/INDEX.md` から対応する手順を確認します。

## Claude Code Settings

このテンプレートでは、追加の permission や hooks をデフォルトでは付与しません。
`.claude/settings.json` は、プロジェクトで Claude Code の権限、hooks、環境変数設定が必要になったときだけ追加します。
