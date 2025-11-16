---
description: tasks.md に定義されたすべてのタスクを処理・実行して実装計画を実行する
---

## ユーザー入力

```text
$ARGUMENTS
```

続行する前に、ユーザー入力を**必ず**考慮してください（空でない場合）。

## 概要

1. リポジトリルートから `.specify/scripts/bash/check-prerequisites.sh --json --require-tasks --include-tasks` を実行し、FEATURE_DIR と AVAILABLE_DOCS リストを解析します。すべてのパスは絶対パスである必要があります。"I'm Groot" のような引数内のシングルクォートには、エスケープ構文を使用してください: 例 'I'\''m Groot' （または可能であればダブルクォート: "I'm Groot"）。

2. **チェックリストステータスの確認**（FEATURE_DIR/checklists/ が存在する場合）:
   - checklists/ ディレクトリ内のすべてのチェックリストファイルをスキャン
   - 各チェックリストについて、以下をカウント:
     - 合計項目数: `- [ ]` または `- [X]` または `- [x]` にマッチするすべての行
     - 完了項目数: `- [X]` または `- [x]` にマッチする行
     - 未完了項目数: `- [ ]` にマッチする行
   - ステータステーブルを作成:

     ```text
     | Checklist | Total | Completed | Incomplete | Status |
     |-----------|-------|-----------|------------|--------|
     | ux.md     | 12    | 12        | 0          | ✓ PASS |
     | test.md   | 8     | 5         | 3          | ✗ FAIL |
     | security.md | 6   | 6         | 0          | ✓ PASS |
     ```

   - 全体のステータスを計算:
     - **PASS**: すべてのチェックリストの未完了項目が 0
     - **FAIL**: 1 つ以上のチェックリストに未完了項目がある

   - **チェックリストに未完了項目がある場合**:
     - 未完了項目数を含むテーブルを表示
     - **停止**して質問: "Some checklists are incomplete. Do you want to proceed with implementation anyway? (yes/no)"
     - 続行前にユーザーの応答を待つ
     - ユーザーが "no" または "wait" または "stop" と言った場合、実行を停止
     - ユーザーが "yes" または "proceed" または "continue" と言った場合、ステップ 3 に進む

   - **すべてのチェックリストが完了している場合**:
     - すべてのチェックリストが合格したことを示すテーブルを表示
     - 自動的にステップ 3 に進む

3. 実装コンテキストのロードと分析:
   - **必須**: 完全なタスクリストと実行計画のために tasks.md を読み込む
   - **必須**: 技術スタック、アーキテクチャ、ファイル構造のために plan.md を読み込む
   - **存在する場合**: エンティティとリレーションシップのために data-model.md を読み込む
   - **存在する場合**: API 仕様とテスト要件のために contracts/ を読み込む
   - **存在する場合**: 技術的決定と制約のために research.md を読み込む
   - **存在する場合**: 統合シナリオのために quickstart.md を読み込む

4. **プロジェクトセットアップの検証**:
   - **必須**: 実際のプロジェクトセットアップに基づいて、無視ファイルを作成/検証:

   **検出と作成ロジック**:
   - 以下のコマンドが成功するか確認して、リポジトリが git リポジトリかどうかを判定（成功した場合、.gitignore を作成/検証）:

     ```sh
     git rev-parse --git-dir 2>/dev/null
     ```

   - Dockerfile* が存在するか、plan.md に Docker がある場合 → .dockerignore を作成/検証
   - .eslintrc* が存在する場合 → .eslintignore を作成/検証
   - eslint.config.* が存在する場合 → config の `ignores` エントリが必要なパターンをカバーしていることを確認
   - .prettierrc* が存在する場合 → .prettierignore を作成/検証
   - .npmrc または package.json が存在する場合 → .npmignore を作成/検証（公開する場合）
   - terraform ファイル (*.tf) が存在する場合 → .terraformignore を作成/検証
   - .helmignore が必要な場合（helm チャートが存在する場合） → .helmignore を作成/検証

   **無視ファイルが既に存在する場合**: 必須パターンが含まれているか確認し、不足している重要なパターンのみを追加
   **無視ファイルが存在しない場合**: 検出された技術のための完全なパターンセットで作成

   **技術別の共通パターン**（plan.md の技術スタックより）:
   - **Node.js/JavaScript/TypeScript**: `node_modules/`, `dist/`, `build/`, `*.log`, `.env*`
   - **Python**: `__pycache__/`, `*.pyc`, `.venv/`, `venv/`, `dist/`, `*.egg-info/`
   - **Java**: `target/`, `*.class`, `*.jar`, `.gradle/`, `build/`
   - **C#/.NET**: `bin/`, `obj/`, `*.user`, `*.suo`, `packages/`
   - **Go**: `*.exe`, `*.test`, `vendor/`, `*.out`
   - **Ruby**: `.bundle/`, `log/`, `tmp/`, `*.gem`, `vendor/bundle/`
   - **PHP**: `vendor/`, `*.log`, `*.cache`, `*.env`
   - **Rust**: `target/`, `debug/`, `release/`, `*.rs.bk`, `*.rlib`, `*.prof*`, `.idea/`, `*.log`, `.env*`
   - **Kotlin**: `build/`, `out/`, `.gradle/`, `.idea/`, `*.class`, `*.jar`, `*.iml`, `*.log`, `.env*`
   - **C++**: `build/`, `bin/`, `obj/`, `out/`, `*.o`, `*.so`, `*.a`, `*.exe`, `*.dll`, `.idea/`, `*.log`, `.env*`
   - **C**: `build/`, `bin/`, `obj/`, `out/`, `*.o`, `*.a`, `*.so`, `*.exe`, `Makefile`, `config.log`, `.idea/`, `*.log`, `.env*`
   - **Swift**: `.build/`, `DerivedData/`, `*.swiftpm/`, `Packages/`
   - **R**: `.Rproj.user/`, `.Rhistory`, `.RData`, `.Ruserdata`, `*.Rproj`, `packrat/`, `renv/`
   - **ユニバーサル**: `.DS_Store`, `Thumbs.db`, `*.tmp`, `*.swp`, `.vscode/`, `.idea/`

   **ツール固有のパターン**:
   - **Docker**: `node_modules/`, `.git/`, `Dockerfile*`, `.dockerignore`, `*.log*`, `.env*`, `coverage/`
   - **ESLint**: `node_modules/`, `dist/`, `build/`, `coverage/`, `*.min.js`
   - **Prettier**: `node_modules/`, `dist/`, `build/`, `coverage/`, `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`
   - **Terraform**: `.terraform/`, `*.tfstate*`, `*.tfvars`, `.terraform.lock.hcl`
   - **Kubernetes/k8s**: `*.secret.yaml`, `secrets/`, `.kube/`, `kubeconfig*`, `*.key`, `*.crt`

5. tasks.md の構造を解析し、以下を抽出:
   - **タスクフェーズ**: Setup、Tests、Core、Integration、Polish
   - **タスク依存関係**: 順次実行 vs 並列実行のルール
   - **タスク詳細**: ID、説明、ファイルパス、並列マーカー [P]
   - **実行フロー**: 順序と依存関係の要件

6. タスク計画に従って実装を実行:
   - **フェーズごとの実行**: 次のフェーズに進む前に各フェーズを完了
   - **依存関係の尊重**: 順次タスクを順番に実行し、並列タスク [P] は同時に実行可能
   - **TDD アプローチに従う**: 対応する実装タスクの前にテストタスクを実行
   - **ファイルベースの調整**: 同じファイルに影響するタスクは順次実行する必要がある
   - **検証チェックポイント**: 続行する前に各フェーズの完了を検証

7. 実装実行のルール:
   - **セットアップを最初に**: プロジェクト構造、依存関係、設定を初期化
   - **コードの前にテスト**: コントラクト、エンティティ、統合シナリオのテストを書く必要がある場合
   - **コア開発**: モデル、サービス、CLI コマンド、エンドポイントを実装
   - **統合作業**: データベース接続、ミドルウェア、ロギング、外部サービス
   - **仕上げと検証**: ユニットテスト、パフォーマンス最適化、ドキュメント

8. 進捗追跡とエラーハンドリング:
   - 各タスク完了後に進捗を報告
   - 非並列タスクが失敗した場合は実行を停止
   - 並列タスク [P] の場合、成功したタスクは続行し、失敗したタスクを報告
   - デバッグのためのコンテキストを含む明確なエラーメッセージを提供
   - 実装を続行できない場合は次のステップを提案
   - **重要** 完了したタスクについては、必ずタスクファイルで [X] としてマークオフしてください。

9. 完了検証:
   - すべての必須タスクが完了していることを確認
   - 実装された機能が元の仕様と一致していることを確認
   - テストが合格し、カバレッジが要件を満たしていることを検証
   - 実装が技術計画に従っていることを確認
   - 完了した作業の要約とともに最終ステータスを報告

注意: このコマンドは、tasks.md に完全なタスク分解が存在することを前提としています。タスクが不完全または欠落している場合は、まず `/speckit.tasks` を実行してタスクリストを再生成することを提案してください。
