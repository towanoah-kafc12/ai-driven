# Implementation Policy Decision

## 2026-06-14: 実装方針テンプレートを01に追加する

- 項番: 1
- 内容:
  - `docs/` 配下のナンバリングをずらし、`docs/01_implementation` を使う技術や実装方針を保存する場所として追加する。
  - 既存の `pending`、`decision`、`review` はそれぞれ `docs/02_pending`、`docs/03_decision`、`docs/04_review` に変更する。
  - `docs/01_implementation/template_implementation.md` を追加する。
- 判断理由:
  - アイデア詳細化後、実装前に採用技術、設計方針、スコープ、テスト方針をある程度確定させておくことで、AIエージェントが実装時に同じ前提を参照しやすくするため。
  - pending / decision だけでは、技術選定や実装方針のような「実装前提」を一覧しづらいため。

### 対応が必要なファイル

- `docs/01_implementation/template_implementation.md`
- `docs/02_pending/template_pending.md`
- `docs/03_decision/template_decision.md`
- `docs/04_review/template_review.md`
- `CLAUDE.md`
- `AGENTS.md`

### 関連情報

- `docs/00_idea.md`
