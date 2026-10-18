# CLAUDE.md（Claude Code の入口）

作業ルールは他のコーディングエージェントと共用するため、[AGENTS.md](AGENTS.md) に集約しています。

@AGENTS.md

このファイルには、Claude Code 固有かつ他のエージェントと共有しない設定だけを追記してください。
作業範囲ルール・規約の所在など、エージェント共通の内容は `AGENTS.md` 側に書きます。

> NOTE:
> `/speckit.plan` の手順 3（`.specify/scripts/bash/update-agent-context.sh`）がこのファイルに
> `## Active Technologies` と `## Recent Changes` を追記します。既存の行は読み直して書き戻される
> ため上の `@AGENTS.md` は保持されますが、spec-kit が入れた技術スタックの要約とこの入口が
> 同じファイルに同居することになります。
>
> このファイルは Git 管理下にあり、チームで共有されます。個人のマシンでしか使わない設定は
> `CLAUDE.local.md` に置いてください（`.gitignore` で追跡対象から外してあります）。
