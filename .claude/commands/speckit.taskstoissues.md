---
description: 利用可能なデザイン成果物に基づいて、既存のタスクを実行可能な依存関係順の GitHub イシューに変換します。
tools: ['github/github-mcp-server/issue_write']
---

## ユーザー入力

```text
$ARGUMENTS
```

続行する前に、ユーザー入力を**必ず**考慮してください（空でない場合）。

## 概要

1. リポジトリルートから `.specify/scripts/bash/check-prerequisites.sh --json --require-tasks --include-tasks` を実行し、FEATURE_DIR と AVAILABLE_DOCS リストを解析します。すべてのパスは絶対パスでなければなりません。「I'm Groot」のような引数内のシングルクォートには、エスケープ構文を使用してください：例 'I'\''m Groot'（可能であればダブルクォートを使用："I'm Groot"）。
1. 実行されたスクリプトから、**tasks** へのパスを抽出します。
1. 以下を実行して Git リモートを取得します：

```bash
git config --get remote.origin.url
```

**リモートが GitHub URL の場合のみ次のステップに進んでください**

1. リスト内の各タスクに対して、GitHub MCP サーバーを使用して、Git リモートに対応するリポジトリに新しいイシューを作成します。

**いかなる状況においても、リモート URL と一致しないリポジトリにイシューを作成してはなりません**
