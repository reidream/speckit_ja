---
description: 自然言語の機能説明から機能仕様書を作成または更新します。
handoffs:
  - label: 技術計画を構築
    agent: speckit.plan
    prompt: 仕様の計画を作成します。使用している技術は...
  - label: 仕様要件を明確化
    agent: speckit.clarify
    prompt: 仕様要件を明確化
    send: true
---

## ユーザー入力

```text
$ARGUMENTS
```

続行する前に、ユーザー入力を**必ず**考慮してください（空でない場合）。

## 概要

トリガーメッセージで `/speckit.specify` の後にユーザーが入力したテキスト**が**機能説明です。以下に `$ARGUMENTS` が文字通り表示されていても、この会話で常に利用可能であると仮定してください。ユーザーが空のコマンドを提供した場合を除き、繰り返しを求めないでください。

その機能説明を使用して、次の手順を実行します:

1. **簡潔な短縮名を生成**（2〜4語）:
   - 機能説明を分析し、最も意味のあるキーワードを抽出
   - 機能の本質を捉える2〜4語の短縮名を作成
   - 可能な限り動作-名詞形式を使用（例: "add-user-auth"、"fix-payment-bug"）
   - 技術用語や頭字語を保持（OAuth2、API、JWTなど）
   - 簡潔に保ちながら、一目で機能を理解できる程度に説明的に
   - 例:
     - "ユーザー認証を追加したい" → "user-auth"
     - "APIのOAuth2統合を実装" → "oauth2-api-integration"
     - "アナリティクス用のダッシュボードを作成" → "analytics-dashboard"
     - "決済処理のタイムアウトバグを修正" → "fix-payment-timeout"

2. **新規作成前に既存ブランチを確認**:

   a. まず、最新情報を取得するためにすべてのリモートブランチをフェッチ:
      ```bash
      git fetch --all --prune
      ```

   b. 短縮名に対するすべてのソースから最大の機能番号を検索:
      - リモートブランチ: `git ls-remote --heads origin | grep -E 'refs/heads/[0-9]+-<short-name>$'`
      - ローカルブランチ: `git branch | grep -E '^[* ]*[0-9]+-<short-name>$'`
      - Specsディレクトリ: `specs/[0-9]+-<short-name>` に一致するディレクトリを確認

   c. 次に利用可能な番号を決定:
      - すべての3つのソースからすべての番号を抽出
      - 最大番号Nを検索
      - 新しいブランチ番号にはN+1を使用

   d. 計算された番号と短縮名でスクリプト `.specify/scripts/bash/create-new-feature.sh --json "$ARGUMENTS"` を実行:
      - 機能説明と共に `--number N+1` と `--short-name "your-short-name"` を渡す
      - Bashの例: `.specify/scripts/bash/create-new-feature.sh --json "$ARGUMENTS" --json --number 5 --short-name "user-auth" "Add user authentication"`
      - PowerShellの例: `.specify/scripts/bash/create-new-feature.sh --json "$ARGUMENTS" -Json -Number 5 -ShortName "user-auth" "Add user authentication"`

   **重要**:
   - 最大番号を見つけるために、すべての3つのソース（リモートブランチ、ローカルブランチ、specsディレクトリ）を確認
   - 正確な短縮名パターンを持つブランチ/ディレクトリのみを一致
   - この短縮名で既存のブランチ/ディレクトリが見つからない場合は、番号1から開始
   - このスクリプトは機能ごとに1回のみ実行する必要があります
   - JSONは端末に出力として提供されます - 実際に探しているコンテンツを取得するために常に参照してください
   - JSON出力にはBRANCH_NAMEとSPEC_FILEパスが含まれます
   - "I'm Groot" のような引数内のシングルクォートには、エスケープ構文を使用: 例 'I'\''m Groot'（可能であればダブルクォートを使用: "I'm Groot"）

3. `.specify/templates/spec-template.md` を読み込んで、必須セクションを理解します。

4. 次の実行フローに従います:

    1. 入力からユーザー説明を解析
       空の場合: エラー "機能説明が提供されていません"
    2. 説明から主要概念を抽出
       識別: アクター、アクション、データ、制約
    3. 不明確な側面について:
       - コンテキストと業界標準に基づいて情報に基づいた推測を行う
       - 次の場合のみ [NEEDS CLARIFICATION: 具体的な質問] でマーク:
         - 選択が機能スコープまたはユーザーエクスペリエンスに大きな影響を与える
         - 異なる影響を持つ複数の妥当な解釈が存在する
         - 妥当なデフォルトが存在しない
       - **制限: [NEEDS CLARIFICATION] マーカーは合計最大3つ**
       - 影響による優先順位付け: スコープ > セキュリティ/プライバシー > ユーザーエクスペリエンス > 技術的詳細
    4. ユーザーシナリオとテストセクションを記入
       明確なユーザーフローがない場合: エラー "ユーザーシナリオを決定できません"
    5. 機能要件を生成
       各要件はテスト可能である必要があります
       未指定の詳細には妥当なデフォルトを使用（前提条件セクションに仮定を文書化）
    6. 成功基準を定義
       測定可能で技術に依存しない成果を作成
       定量的メトリック（時間、パフォーマンス、ボリューム）と定性的測定（ユーザー満足度、タスク完了）の両方を含める
       各基準は実装の詳細なしで検証可能である必要があります
    7. 主要なエンティティを識別（データが関与する場合）
    8. 戻り値: 成功（仕様は計画の準備完了）

5. テンプレート構造を使用してSPEC_FILEに仕様を記述し、セクションの順序と見出しを保持しながら、プレースホルダーを機能説明（引数）から導出された具体的な詳細に置き換えます。

6. **仕様品質検証**: 初期仕様を記述した後、品質基準に対して検証します:

   a. **仕様品質チェックリストを作成**: 次の検証項目を含むチェックリストテンプレート構造を使用して、`FEATURE_DIR/checklists/requirements.md` にチェックリストファイルを生成:

      ```markdown
      # Specification Quality Checklist: [FEATURE NAME]

      **Purpose**: Validate specification completeness and quality before proceeding to planning
      **Created**: [DATE]
      **Feature**: [Link to spec.md]

      ## Content Quality

      - [ ] No implementation details (languages, frameworks, APIs)
      - [ ] Focused on user value and business needs
      - [ ] Written for non-technical stakeholders
      - [ ] All mandatory sections completed

      ## Requirement Completeness

      - [ ] No [NEEDS CLARIFICATION] markers remain
      - [ ] Requirements are testable and unambiguous
      - [ ] Success criteria are measurable
      - [ ] Success criteria are technology-agnostic (no implementation details)
      - [ ] All acceptance scenarios are defined
      - [ ] Edge cases are identified
      - [ ] Scope is clearly bounded
      - [ ] Dependencies and assumptions identified

      ## Feature Readiness

      - [ ] All functional requirements have clear acceptance criteria
      - [ ] User scenarios cover primary flows
      - [ ] Feature meets measurable outcomes defined in Success Criteria
      - [ ] No implementation details leak into specification

      ## Notes

      - Items marked incomplete require spec updates before `/speckit.clarify` or `/speckit.plan`
      ```

   b. **検証チェックを実行**: 各チェックリスト項目に対して仕様をレビュー:
      - 各項目について、合格か不合格かを判断
      - 見つかった具体的な問題を文書化（関連する仕様セクションを引用）

   c. **検証結果の処理**:

      - **すべての項目が合格の場合**: チェックリストを完了としてマークし、ステップ6に進む

      - **項目が不合格の場合（[NEEDS CLARIFICATION]を除く）**:
        1. 不合格項目と具体的な問題をリスト化
        2. 各問題に対処するために仕様を更新
        3. すべての項目が合格するまで検証を再実行（最大3回の反復）
        4. 3回の反復後も不合格の場合、チェックリストノートに残りの問題を文書化し、ユーザーに警告

      - **[NEEDS CLARIFICATION] マーカーが残っている場合**:
        1. 仕様からすべての [NEEDS CLARIFICATION: ...] マーカーを抽出
        2. **制限チェック**: 3つ以上のマーカーが存在する場合、最も重要な3つのみを保持（スコープ/セキュリティ/UX影響による）し、残りについては情報に基づいた推測を行う
        3. 必要な各明確化（最大3つ）について、次の形式でユーザーにオプションを提示:

           ```markdown
           ## Question [N]: [Topic]

           **Context**: [Quote relevant spec section]

           **What we need to know**: [Specific question from NEEDS CLARIFICATION marker]

           **Suggested Answers**:

           | Option | Answer | Implications |
           |--------|--------|--------------|
           | A      | [First suggested answer] | [What this means for the feature] |
           | B      | [Second suggested answer] | [What this means for the feature] |
           | C      | [Third suggested answer] | [What this means for the feature] |
           | Custom | Provide your own answer | [Explain how to provide custom input] |

           **Your choice**: _[Wait for user response]_
           ```

        4. **重要 - テーブルのフォーマット**: マークダウンテーブルが適切にフォーマットされていることを確認:
           - パイプを揃えた一貫したスペーシングを使用
           - 各セルにはコンテンツの周りにスペースが必要: `| Content |` であり `|Content|` ではない
           - ヘッダーセパレーターには少なくとも3つのダッシュが必要: `|--------|`
           - テーブルがマークダウンプレビューで正しくレンダリングされることをテスト
        5. 質問を順番に番号付け（Q1、Q2、Q3 - 合計最大3つ）
        6. 応答を待つ前にすべての質問を一緒に提示
        7. ユーザーがすべての質問に対する選択で応答するのを待つ（例: "Q1: A、Q2: Custom - [詳細]、Q3: B"）
        8. 各 [NEEDS CLARIFICATION] マーカーをユーザーが選択または提供した回答で置き換えて仕様を更新
        9. すべての明確化が解決された後、検証を再実行

   d. **チェックリストを更新**: 各検証反復後、現在の合格/不合格ステータスでチェックリストファイルを更新

7. ブランチ名、仕様ファイルパス、チェックリスト結果、および次のフェーズ（`/speckit.clarify` または `/speckit.plan`）の準備状況を報告して完了します。

**注意:** スクリプトは新しいブランチを作成してチェックアウトし、記述前に仕様ファイルを初期化します。

## 一般的なガイドライン

## クイックガイドライン

- ユーザーが**何を**必要とし、**なぜ**必要なのかに焦点を当てる。
- 実装方法を避ける（技術スタック、API、コード構造なし）。
- 開発者ではなく、ビジネス関係者向けに記述。
- 仕様に埋め込まれたチェックリストは作成しないでください。それは別のコマンドになります。

### セクション要件

- **必須セクション**: すべての機能で完了する必要があります
- **オプションセクション**: 機能に関連する場合のみ含める
- セクションが適用されない場合は、完全に削除する（"N/A"のまま残さない）

### AI生成用

ユーザープロンプトからこの仕様を作成する場合:

1. **情報に基づいた推測を行う**: コンテキスト、業界標準、一般的なパターンを使用してギャップを埋める
2. **仮定を文書化**: 前提条件セクションに妥当なデフォルトを記録
3. **明確化を制限**: 最大3つの [NEEDS CLARIFICATION] マーカー - 次のような重要な決定にのみ使用:
   - 機能スコープまたはユーザーエクスペリエンスに大きな影響を与える
   - 異なる影響を持つ複数の妥当な解釈がある
   - 妥当なデフォルトが存在しない
4. **明確化の優先順位付け**: スコープ > セキュリティ/プライバシー > ユーザーエクスペリエンス > 技術的詳細
5. **テスターのように考える**: すべての曖昧な要件は「テスト可能で曖昧さがない」チェックリスト項目で不合格となるべき
6. **明確化が必要な一般的な領域**（妥当なデフォルトが存在しない場合のみ）:
   - 機能のスコープと境界（特定のユースケースを含む/除外）
   - ユーザータイプと権限（複数の競合する解釈が可能な場合）
   - セキュリティ/コンプライアンス要件（法的/財務的に重要な場合）

**妥当なデフォルトの例**（これらについては尋ねない）:

- データ保持: ドメインの業界標準プラクティス
- パフォーマンス目標: 特に指定がない限り、標準的なウェブ/モバイルアプリの期待値
- エラー処理: 適切なフォールバックを備えたユーザーフレンドリーなメッセージ
- 認証方法: ウェブアプリの標準的なセッションベースまたはOAuth2
- 統合パターン: 特に指定がない限りRESTful API

### 成功基準のガイドライン

成功基準は次の条件を満たす必要があります:

1. **測定可能**: 具体的なメトリックを含む（時間、パーセンテージ、カウント、レート）
2. **技術に依存しない**: フレームワーク、言語、データベース、ツールの言及なし
3. **ユーザー中心**: システム内部ではなく、ユーザー/ビジネスの観点から成果を説明
4. **検証可能**: 実装の詳細を知らなくてもテスト/検証可能

**良い例**:

- "ユーザーは3分以内にチェックアウトを完了できる"
- "システムは10,000の同時ユーザーをサポートする"
- "検索の95%が1秒以内に結果を返す"
- "タスク完了率が40%向上する"

**悪い例**（実装に焦点を当てている）:

- "APIレスポンスタイムが200ms未満"（技術的すぎる、「ユーザーは結果を即座に見る」を使用）
- "データベースは1000 TPSを処理できる"（実装の詳細、ユーザー向けメトリックを使用）
- "Reactコンポーネントが効率的にレンダリング"（フレームワーク固有）
- "Redisキャッシュヒット率が80%以上"（技術固有）
