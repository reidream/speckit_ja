---
description: プランテンプレートを使用して実装計画ワークフローを実行し、設計成果物を生成します。
handoffs:
  - label: タスクを作成
    agent: speckit.tasks
    prompt: プランをタスクに分解
    send: true
  - label: チェックリストを作成
    agent: speckit.checklist
    prompt: 以下のドメインのチェックリストを作成...
---

## ユーザー入力

```text
$ARGUMENTS
```

処理を進める前に、ユーザー入力を**必ず**考慮してください（空でない場合）。

## 概要

1. **セットアップ**: リポジトリのルートから `.specify/scripts/bash/setup-plan.sh --json` を実行し、FEATURE_SPEC、IMPL_PLAN、SPECS_DIR、BRANCH の JSON を解析します。"I'm Groot" のような引数内のシングルクォートには、エスケープ構文を使用してください: 例 'I'\''m Groot' （または可能であればダブルクォートを使用: "I'm Groot"）。

2. **コンテキストの読み込み**: FEATURE_SPEC と `.specify/memory/constitution.md` を読み込みます。IMPL_PLAN テンプレート（既にコピー済み）を読み込みます。

3. **プランワークフローの実行**: IMPL_PLAN テンプレートの構造に従って以下を実施:
   - 技術的コンテキストを記入（不明点は "NEEDS CLARIFICATION" とマーク）
   - constitution から憲章チェックセクションを記入
   - ゲートを評価（違反が正当化されない場合は ERROR）
   - フェーズ 0: research.md を生成（すべての NEEDS CLARIFICATION を解決）
   - フェーズ 1: data-model.md、contracts/、quickstart.md を生成
   - フェーズ 1: エージェントスクリプトを実行してエージェントコンテキストを更新
   - 設計後に憲章チェックを再評価

4. **停止と報告**: フェーズ 2 計画後にコマンドを終了します。ブランチ、IMPL_PLAN パス、生成された成果物を報告します。

## フェーズ

### フェーズ 0: アウトライン & リサーチ

1. **上記の技術的コンテキストから不明点を抽出**:
   - 各 NEEDS CLARIFICATION → リサーチタスク
   - 各依存関係 → ベストプラクティスタスク
   - 各統合 → パターンタスク

2. **リサーチエージェントの生成と派遣**:

   ```text
   For each unknown in Technical Context:
     Task: "Research {unknown} for {feature context}"
   For each technology choice:
     Task: "Find best practices for {tech} in {domain}"
   ```

3. **調査結果の統合** を `research.md` に以下の形式で記録:
   - Decision: [選択されたもの]
   - Rationale: [選択理由]
   - Alternatives considered: [評価された他の選択肢]

**出力**: すべての NEEDS CLARIFICATION が解決された research.md

### フェーズ 1: 設計 & 契約

**前提条件:** `research.md` が完了していること

1. **機能仕様からエンティティを抽出** → `data-model.md`:
   - エンティティ名、フィールド、関係性
   - 要件からの検証ルール
   - 該当する場合は状態遷移

2. **機能要件から API 契約を生成**:
   - 各ユーザーアクション → エンドポイント
   - 標準的な REST/GraphQL パターンを使用
   - OpenAPI/GraphQL スキーマを `/contracts/` に出力

3. **エージェントコンテキストの更新**:
   - `.specify/scripts/bash/update-agent-context.sh claude` を実行
   - これらのスクリプトは使用中の AI エージェントを検出
   - 適切なエージェント固有のコンテキストファイルを更新
   - 現在のプランからの新しい技術のみを追加
   - マーカー間の手動追加を保持

**出力**: data-model.md、/contracts/*、quickstart.md、エージェント固有のファイル

## 重要なルール

- 絶対パスを使用
- ゲート失敗または未解決の明確化事項がある場合は ERROR
