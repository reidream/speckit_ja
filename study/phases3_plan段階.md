# Phase 3 (Plan段階) 完全学習サマリー

**学習日**: 2025-11-18  
**学習者**: Leidream  
**対象**: spec-kit の Phase 3 (Plan段階)

---

## 📚 目次

1. [Phase 3 学習内容](#phase-3-学習内容)
2. [Leidreamの学習思考プロセス](#leidreamの学習思考プロセス)
3. [重要な気づきと質問](#重要な気づきと質問)
4. [補足情報](#補足情報)
5. [次のステップへの提案](#次のステップへの提案)

---

## Phase 3 学習内容

### Phase 3 全体像

```
Phase 3 = Plan段階 (計画・設計)

目的:
  実装に必要な設計書を全て作成する

7つのステップ:
  Step 1: セットアップ
  Step 2: コンテキスト読み込み
  Step 3: Technical Context記入
  Step 4: Phase -1 (憲章チェック)
  Step 5: Phase 0 (技術調査)
  Step 6: Phase 1 (詳細設計)
  Step 7: Phase 1後の憲章再チェック
```

---

### Step 1: セットアップスクリプト

**目的:**
```
Phase 3に必要な情報を準備する
```

**スクリプトの処理:**
```bash
1. spec.mdを読む
2. plan.mdの雛形を作る
3. スペックディレクトリのパスを取得
4. ブランチ名を取得
5. JSON形式で返す
```

**返されるJSON:**
```json
{
  "FEATURE_SPEC": "spec.mdの内容",
  "IMPL_PLAN": "plan.mdの雛形",
  "SPECS_DIR": "/path/to/specs/001-feature/",
  "BRANCH": "feature/001-feature-name"
}
```

**学習ポイント:**
- スクリプトが自動で情報を集める
- AIエージェントはこの情報を使って作業する
- 人間は実行するだけ

---

### Step 2: コンテキスト読み込み

**目的:**
```
必要な情報を全て読み込む
```

**読み込むもの:**
```
1. spec.md (何を作るか)
2. constitution.md (守るべきルール)
3. CLAUDE.md (過去のプロジェクト情報)
4. plan.md (既存の計画書)
```

**なぜ必要？**
```
情報なし → 判断できない
情報あり → 適切に判断できる
```

---

### Step 3: Technical Context記入

**目的:**
```
plan.mdのTechnical Contextセクションを埋める
```

**記入内容:**
```markdown
## Technical Context

Language/Version: Python 3.11
Primary Dependencies: FastAPI
Storage: SQLite
Testing: pytest
Target Platform: Linux server
Project Type: web
Performance Goals: <3s login
Constraints: 1000 concurrent
Scale/Scope: 1000 users
```

**重要なルール:**
```
わかる項目: すぐ記入
わからない項目: NEEDS CLARIFICATION

例:
Language/Version: Python 3.11 ✅
Primary Dependencies: NEEDS CLARIFICATION ❓
```

**情報源の優先順位:**
```
1. constitution.md (最優先)
2. CLAUDE.md (過去の実績)
3. spec.md (要件から推測)
4. 技術的ベストプラクティス
```

---

### Step 4: Phase -1 (憲章チェック)

**目的:**
```
Technical Contextが憲章に違反していないかチェック
```

**タイミング:**
```
Phase 0より前
= Phase -1
```

**チェック内容:**
```
1. 技術制約
   例: 「SQLiteのみ」に従っているか

2. テスト要件
   例: 「テスト必須」が計画されているか

3. 性能要件
   例: 「200ms以内」を目指しているか

4. セキュリティ要件
   例: 「認証必須」になっているか
```

**3つの結果:**
```
✅ PASSED (合格)
   → Phase 0へ進む

⚠️ WARNING (警告)
   → Phase 0へ進む (Phase 0で解決)

❌ FAILED (違反)
   → 停止！修正が必要
```

**重要な理由:**
```
早期チェック = 早期修正

Phase -1でエラー発見: 1分で修正
Phase 1でエラー発見: 2時間の手戻り
Phase 5でエラー発見: 全部作り直し

→ 早ければ早いほど損失が少ない
```

---

### Step 5: Phase 0 (技術調査)

**目的:**
```
「NEEDS CLARIFICATION」を全て解決する
```

**処理の流れ:**
```
1. 不明点を抽出
   「？」が何個あるか数える

2. 各不明点を調査
   - spec.mdを読む
   - 過去のプロジェクトを見る
   - 技術ドキュメントを調べる

3. 決定を記録
   research.mdに書く

4. plan.mdを更新
   「？」を答えで埋める

5. 次の不明点へ
   全ての「？」が消えるまで繰り返し
```

**調査してもわからない場合:**
```
ユーザーに質問する

例:
「デプロイ先はAWS、GCP、Azureのどれにしますか？
 推奨: AWS (理由: スケーラビリティ)」
```

**成果物: research.md**
```markdown
## Primary Dependencies

### Decision
FastAPI + python-jose + passlib

### Rationale
- FastAPI: 非同期、高速、型ヒント
- python-jose: JWT実装
- passlib: パスワードハッシュ化

### Alternatives Considered
Flask: 
  ❌ 同期処理のみ
Django:
  ❌ 重すぎる

### References
- FastAPI docs
- spec.md の「1000同時接続」要件
```

**research.mdとplan.mdの違い:**
```
research.md (調査ノート):
  - 詳しい
  - 調査の過程
  - 理由を説明
  - 代替案も記録
  
plan.md (設計書):
  - 簡潔
  - 答えだけ
  - すぐわかる
```

**Phase 0完了条件:**
```
✅ NEEDS CLARIFICATION が0個
✅ research.md が作成されている
✅ plan.md が完全に埋まっている
```

---

### Step 6: Phase 1 (詳細設計)

**目的:**
```
実装に必要な設計書を全て作る
```

**3つのファイルを作成:**

#### 6-1: data-model.md (データベース設計)

```markdown
# Data Model: User Authentication

## User Entity

### Fields
- id: UUID (Primary Key)
- username: String (max 50 chars, unique)
- email: String (max 100 chars, unique)
- password_hash: String (bcrypt)
- created_at: DateTime (auto)
- updated_at: DateTime (auto)
- is_active: Boolean (default: true)

### Indexes
- username (unique)
- email (unique)
- created_at (for sorting)

### Validation Rules
- username: 英数字のみ、3-50文字
- email: 有効なメール形式
- password: 最低8文字

### Relationships
- User has many Sessions (1:N)
```

**役割:**
```
spec.mdのエンティティ:
  「User」「Message」(簡単なリスト)
  
  ↓ 詳細化
  
data-model.md:
  型、長さ、制約、リレーション (完全な設計)
```

---

#### 6-2: contracts/ (API設計)

```yaml
openapi: 3.0.0
paths:
  /api/v1/auth/login:
    post:
      summary: ログイン
      
      # 受け取り (リクエスト)
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                username:
                  type: string
                  example: "user123"
                password:
                  type: string
                  example: "password123"
              required:
                - username
                - password
      
      # 返すもの (レスポンス)
      responses:
        200:
          description: ログイン成功
          content:
            application/json:
              schema:
                type: object
                properties:
                  token:
                    type: string
                  user_id:
                    type: string
        401:
          description: 認証失敗
```

**役割:**
```
API契約書

定義する内容:
  ✅ 受け取り (requestBody)
  ✅ 返すもの (responses)
  ✅ エラー (error responses)
  
使うタイミング:
  ✅ Webアプリ
  ✅ モバイルアプリ
  ✅ API通信があるもの
  
使わないとき:
  ❌ CLIツール
  ❌ 単体スクリプト
  ❌ API通信がないもの
```

**なぜ必要？**
```
フロントエンドとバックエンドが
並行開発できる

フロントエンド:
  「login.yaml見れば、
   何を送って何が返るかわかる」
   
バックエンド:
  「login.yaml通りに実装すればいい」
```

---

#### 6-3: quickstart.md (開発ガイド)

```markdown
# Quickstart: User Authentication

## Prerequisites
- Python 3.11+
- SQLite
- pip

## Setup

### 1. Install dependencies
```bash
pip install fastapi uvicorn python-jose passlib
```

### 2. Initialize database
```bash
python scripts/init_db.py
```

### 3. Run server
```bash
uvicorn app.main:app --reload
```

## Testing Your First Login

### 1. Register a user
```bash
curl -X POST http://localhost:8000/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"username": "test", "password": "test123"}'
```

### 2. Login
```bash
curl -X POST http://localhost:8000/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username": "test", "password": "test123"}'
```
```

**役割:**
```
新しい開発者のためのガイド

目的:
  5分でセットアップ完了
  すぐに開発開始

なかったら:
  あちこち調べる (1時間)
```

---

### Step 7: Phase 1終了後の憲章再チェック

**目的:**
```
詳細設計が憲章に合っているか確認
```

**Phase -1との違い:**
```
Phase -1 (1回目):
  技術選択をチェック
  「SQLite使う？」→ OK
  
Phase 1後 (2回目):
  詳細設計をチェック
  「パスワードハッシュ化してる？」→ OK
  「認証必須になってる？」→ OK
  「テスト手順ある？」→ OK
```

**チェック項目:**
```
1. data-model.md
   - パスワードハッシュ化
   - データ型の適切さ
   
2. contracts/
   - 認証の有無
   - エラーハンドリング
   
3. quickstart.md
   - テストの手順
   - カバレッジ目標
```

**なぜ2回チェック？**
```
理由1: 段階的な確認
  Phase -1: 大きな方向性
  Phase 1後: 詳細

理由2: 早期発見
  Phase 1で決める
  → すぐチェック
  → 問題を早期発見

理由3: 二重の安全網
  1回目の網 + 2回目の網
  → 確実に憲章を守る
```

---

## Leidreamの学習思考プロセス

### 学習の特徴

**1. 本質的な質問**
```
「テスト80%とは？」
「contracts これは受け取りではなく、与えるほうのはなし？」
「何を作るときに使うの？」
「それでもわからない場合は？userに聞く流れ？」

→ 表面的な理解では満足しない
→ 根本から理解しようとする姿勢
```

**2. 段階的な理解の深化**
```
第1段階: 「わからない」と正直に言える
第2段階: より具体的な質問に変わる
第3段階: 自分の理解を確認する質問
第4段階: 応用的な質問

例:
「全体的にわかってない」
  ↓
「plan.mdの話？」
  ↓
「plan.mdを埋めていくけど、その中で？や空欄があればってはなし？」
  ↓
「それでもわからない場合は？userに聞く流れ？」
```

**3. 簡潔さへのこだわり**
```
最初の説明: エピソード風で長い
  ↓
Leidream: 「わかってない」
  ↓
要求: シンプルな説明

→ 本質だけを理解したい
→ 装飾的な説明は不要
```

**4. 実用性重視**
```
「何を作るときに使うの？」

→ 理論ではなく実践
→ いつ使うのかを知りたい
```

### 理解の転換点

**転換点1: plan.mdの位置づけ**
```
Before: Phase 0が何かわからない
After: 「plan.mdの？を埋める作業」と理解

キーフレーズ:
  「plan.mdの話？」
```

**転換点2: research.mdの役割**
```
Before: research.mdとplan.mdの違いがわからない
After: 「調査ノート vs 設計書」と理解

キーフレーズ:
  「plan.mdの埋めれなかった内容を、
   research.mdに書いているんだね？」
```

**転換点3: contracts/の用途**
```
Before: contracts/の方向性がわからない
After: 「受け取りと返す、両方を定義」と理解

キーフレーズ:
  「これは受け取りではなく、与えるほうのはなし？」
  「何を作るときに使うの？」
```

**転換点4: ユーザー関与のタイミング**
```
Before: 調査プロセスが不明確
After: 「調査→わからない→ユーザーに質問」の流れを理解

キーフレーズ:
  「それでもわからない場合は？userに聞く流れ？」
```

---

## 重要な気づきと質問

### Leidreamの優れた質問

**質問1: 「テスト80%とは？」**
```
表面的な理解: 「80%カバーする」
深い理解: 「コードの80%がテストで実行される」

この質問により:
  - テストカバレッジの具体的な計算方法を学習
  - Phase -1とPhase 5でのチェックの違いを理解
  - 100%を目指さない理由も把握
```

**質問2: 「contracts これは受け取りではなく、与えるほうのはなし？」**
```
重要な洞察:
  API設計では「方向性」が重要
  
この質問により:
  - requestBodyとresponsesの両方を定義することを理解
  - API契約書の完全性を把握
  - フロントエンドとバックエンドの並行開発が可能になる理由を理解
```

**質問3: 「何を作るときに使うの？」**
```
実用性の確認:
  理論ではなく実践を重視
  
この質問により:
  - contracts/の適用範囲を理解
  - WebアプリとCLIツールの違いを把握
  - 判断基準「HTTPで通信する？」を獲得
```

**質問4: 「それでもわからない場合は？userに聞く流れ？」**
```
プロセスの完全性の確認:
  全てのケースをカバーしているか
  
この質問により:
  - 調査のフローチャート全体を理解
  - エージェントの限界とユーザー関与のタイミングを把握
  - 質問のフォーマットと推奨の重要性を理解
```

### 学習で得られた重要な理解

**1. 段階的なチェックの価値**
```
Phase -1: 技術選択
Phase 1後: 詳細設計

2回チェック = 二重の安全網
早期発見 = コスト削減
```

**2. ドキュメントの役割分担**
```
plan.md: 簡潔な設計書 (実装時に参照)
research.md: 詳細な調査記録 (理由の説明)
data-model.md: データベース設計 (実装の詳細)
contracts/: API契約 (並行開発の基盤)
quickstart.md: 開発ガイド (新メンバーの導入)
```

**3. 「？」の正直さの重要性**
```
無理やり埋める = 間違った選択
「？」でマーク = 正直で健全
Phase 0で解決 = 適切なプロセス
```

**4. ユーザー関与のタイミング**
```
自動で決められる → 決める
推測できる → 推測する
判断が必要 → ユーザーに質問

最大3つまで質問
推奨案を示す
理由を説明する
```

---

## 補足情報

### Phase 3の位置づけ

**spec-kit全体の中で:**
```
Phase 1: 憲章 (Constitution)
  ↓
Phase 2: 仕様 (Specify)
  ↓
Phase 3: 計画 (Plan) ← 今回学習
  ↓
Phase 4: タスク分解 (Tasks)
  ↓
Phase 5: 実装 (Implement)
```

**Phase 3の重要性:**
```
Phase 2までは「何を作るか」
Phase 3は「どう作るか」
Phase 4以降は「作る」

→ Phase 3は設計の要
→ ここで決まったことが実装の基盤
```

---

### 時間配分の目安

**Phase 3全体:**
```
小規模機能: 30-60分
中規模機能: 1-2時間
大規模機能: 3-5時間
```

**各ステップ:**
```
Step 1 (セットアップ): 1分
Step 2 (コンテキスト読み込み): 2-5分
Step 3 (Technical Context): 5-10分
Step 4 (Phase -1): 2-5分
Step 5 (Phase 0): 10-30分
Step 6 (Phase 1): 20-60分
Step 7 (Phase 1後チェック): 2-5分
```

---

### よくある間違いと対策

**間違い1: Step 3で全部埋めようとする**
```
❌ わからないのに無理やり書く
✅ わからないものは「？」でマーク
```

**間違い2: Phase 0をスキップ**
```
❌ 「？」を残したままPhase 1へ
✅ 全ての「？」を解決してから進む
```

**間違い3: research.mdを書かない**
```
❌ 「面倒だから答えだけplan.mdに」
✅ 理由を記録 → 6ヶ月後の自分が感謝
```

**間違い4: 憲章チェックを軽視**
```
❌ 「チェック面倒、スキップ」
✅ 早期チェック = 手戻り防止
```

**間違い5: quickstart.mdを後回し**
```
❌ 「実装後に書けばいい」
✅ 先に書く → 実装方針が明確に
```

---

### Phase 3のベストプラクティス

**1. コンテキストを完全に読む**
```
✅ spec.md 全部
✅ constitution.md 全部
✅ CLAUDE.md 全部

中途半端に読む → 判断ミス
```

**2. 憲章を最優先**
```
技術的には良い選択でも
憲章に合わないならNG

例:
PostgreSQLは良いDB
でも憲章が「SQLiteのみ」
→ SQLiteを選ぶ
```

**3. 過去の実績を活用**
```
CLAUDE.mdに
「001でFastAPI使用」

→ 002もFastAPIにする
→ 技術スタック統一
```

**4. 調査は徹底的に**
```
spec.mdを読む
過去プロジェクトを見る
技術ドキュメントを調べる
それでもダメならユーザーに質問

中途半端な調査 → 間違った選択
```

**5. ドキュメントは詳しく**
```
research.md:
  - Decision (何を)
  - Rationale (なぜ)
  - Alternatives (他の選択肢)
  - References (参考資料)

6ヶ月後に読んでもわかるように
```

---

### テストカバレッジ補足

**カバレッジの種類:**
```
1. 行カバレッジ (Line Coverage)
   一般的な指標
   「何行が実行されたか」

2. 分岐カバレッジ (Branch Coverage)
   より厳密
   「全ての分岐を通ったか」

3. 関数カバレッジ (Function Coverage)
   「全ての関数が呼ばれたか」
```

**80%の意味:**
```
100%は現実的でない理由:
  - エラーハンドリングの全パターン
  - 到達不可能なコード
  - コスト vs 効果

80%が現実的:
  - 主要な処理はカバー
  - 重要なエラーケースもカバー
  - コストと品質のバランス
```

**Phase -1でのチェック:**
```
実測値ではなく「計画の有無」をチェック

✅ Testing: pytest
   → テストを書く計画がある

❌ Testing: なし
   → テストを書く計画がない
```

---

### contracts/ の詳細補足

**OpenAPI 3.0の利点:**
```
1. 標準フォーマット
   業界標準、ツールが豊富

2. 自動ドキュメント生成
   Swagger UIで見やすい

3. 自動テスト生成
   契約通りかテスト可能

4. モックサーバー生成
   フロントエンドが先に開発可能
```

**いつcontracts/を作るべきか:**
```
✅ 作るべき:
  - Webアプリ
  - モバイルアプリ
  - マイクロサービス
  - 外部APIを提供

❌ 不要:
  - CLIツール
  - バッチ処理
  - デスクトップアプリ (オフライン)
  - 内部ライブラリ
```

**contracts/の構造:**
```
contracts/
├── login.yaml        # ログインAPI
├── register.yaml     # 登録API
├── messages.yaml     # メッセージAPI
└── users.yaml        # ユーザーAPI

各ファイル:
  - エンドポイント定義
  - リクエスト形式
  - レスポンス形式
  - エラーレスポンス
  - 認証要件
```

---

## 次のステップへの提案

### 提案1: Phase 4 (Tasks) の学習準備

**Phase 4で学ぶこと:**
```
Phase 3で作った設計書を
実装可能なタスクに分解

例:
plan.md
  ↓ タスク分解
tasks.md:
  - Task 1: データベース初期化
  - Task 2: Userモデル作成
  - Task 3: ログインAPI実装
  - Task 4: テスト作成
```

**Phase 3との関係:**
```
Phase 3: 「何を作るか」を決める
Phase 4: 「どの順番で作るか」を決める

Phase 3が完全 → Phase 4がスムーズ
Phase 3が不完全 → Phase 4で手戻り
```

---

### 提案2: 実践演習

**小規模な機能で練習:**
```
例: 「メッセージ投稿機能」

Phase 3を実践:
  1. Technical Context記入
  2. Phase 0で調査
  3. data-model.md作成
  4. contracts/message.yaml作成
  5. quickstart.md作成
  6. 憲章チェック

目標時間: 30-45分
```

**練習のポイント:**
```
✅ 実際にファイルを作る
✅ research.mdに理由を書く
✅ 憲章を守る
✅ 「？」を正直にマークする
```

---

### 提案3: 自己学習型Memory Agentとの統合

**Phase 3 + Memory Agent:**
```
現在のPhase 0:
  spec.md → 調査 → 決定
  
Memory Agent統合後:
  Phase 0の前に:
    ↓
  Memory Search Agent:
    過去の類似機能を検索
    成功パターンを提案
    失敗パターンを警告
    ↓
  Phase 0:
    提案を参考に調査
    新しい情報を追加
    ↓
  記録:
    timeline.jsonに保存
    次回の参考に
```

**具体的な統合案:**
```
Step 5-0: Memory Search (新規)
  ↓
  過去のプロジェクトから学習
  類似機能を検索
  最大10回繰り返し
  ↓
Step 5: Phase 0
  ↓
  Memory Searchの結果を参考に調査
```

---

### 提案4: チーム開発への応用

**Phase 3をチームで使う:**
```
メンバーA:
  Phase 3を実行
  plan.md, research.md作成
  
メンバーB:
  plan.mdを読む
  → 実装方針がわかる
  
メンバーC:
  contracts/を読む
  → API仕様がわかる
  
新メンバー:
  quickstart.mdを読む
  → 5分でセットアップ完了
```

**ドキュメントの共有:**
```
GitHubで管理:
  specs/001-feature/
    ├── spec.md
    ├── plan.md
    ├── research.md
    ├── data-model.md
    ├── contracts/
    └── quickstart.md

全員が同じ情報を見る
→ 一貫性が保たれる
```

---

### 提案5: 継続的改善

**Phase 3の振り返り:**
```
プロジェクト完了後:
  
1. research.mdを見直す
   「この決定は正しかった？」
   
2. plan.mdを見直す
   「設計は適切だった？」
   
3. quickstart.mdを見直す
   「新メンバーは理解できた？」
   
4. 学んだことを記録
   次回のPhase 3に活かす
```

**改善サイクル:**
```
Phase 3実行
  ↓
実装・テスト
  ↓
振り返り
  ↓
学習を記録
  ↓
次のPhase 3で活用
  ↓
(繰り返し)
```

---

## まとめ

### Phase 3で学んだ核心

**1. Plan = 設計の要**
```
Phase 3で全ての設計を完成させる
実装はPhase 3の設計書通りに進む
```

**2. 段階的なプロセスの価値**
```
Step 3: Technical Context記入
  ↓ 「？」を残してOK
Step 4: Phase -1 (憲章チェック)
  ↓ 大きな方向性を確認
Step 5: Phase 0 (技術調査)
  ↓ 「？」を全て解決
Step 6: Phase 1 (詳細設計)
  ↓ 実装可能な設計書を作成
Step 7: Phase 1後チェック
  ↓ 詳細設計を確認

各段階で確認 → 手戻り防止
```

**3. ドキュメントの役割**
```
plan.md: 設計書 (簡潔)
research.md: 調査記録 (詳細)
data-model.md: DB設計
contracts/: API契約
quickstart.md: 開発ガイド

それぞれが明確な役割を持つ
```

**4. 正直さの重要性**
```
わからない → 「？」でマーク
調査してもダメ → ユーザーに質問
無理やり埋めない → 正確な設計
```

---

### Leidreamの学習成果

**獲得したスキル:**
```
✅ Phase 3の7ステップの理解
✅ 各ファイルの役割の理解
✅ 憲章チェックの重要性の理解
✅ 段階的プロセスの価値の理解
✅ ユーザー関与のタイミングの理解
```

**特に優れた理解:**
```
1. plan.mdとresearch.mdの関係
   「調査ノート vs 設計書」
   
2. contracts/の方向性
   「受け取りと返す、両方」
   
3. contracts/の適用範囲
   「HTTPで通信するかどうか」
   
4. エスカレーションフロー
   「調査 → わからない → ユーザーに質問」
```

**学習スタイルの強み:**
```
✅ 本質的な質問
✅ 段階的な理解の深化
✅ 簡潔さへのこだわり
✅ 実用性重視
✅ 正直なフィードバック
```

---

### 次のステップ

**推奨される学習順序:**
```
1. Phase 3の実践演習
   小規模機能で練習 (30-45分)

2. Phase 4 (Tasks) の学習
   タスク分解の方法

3. Memory Agent統合の検討
   自己学習システムの構築

4. チーム開発への応用
   ドキュメント共有の実践
```

**長期的な目標:**
```
spec-kit完全マスター
  ↓
自己学習型システム構築
  ↓
チーム開発への展開
  ↓
業界への貢献
```

---

## 付録

### Phase 3チェックリスト

```markdown
## Phase 3開始前
□ spec.md が完成している
□ constitution.md が存在する
□ CLAUDE.md が最新

## Step 1: セットアップ
□ スクリプトが正常に実行された
□ JSONが返された
□ plan.mdの雛形ができた

## Step 3: Technical Context
□ わかる項目を全て記入した
□ わからない項目は「？」でマークした
□ 憲章を参照した
□ CLAUDE.mdを参照した

## Step 4: Phase -1
□ 憲章チェックを実行した
□ 違反がなければ次へ
□ 違反があれば修正した

## Step 5: Phase 0
□ 全ての「？」をリストアップした
□ spec.mdから情報収集した
□ 過去のプロジェクトを確認した
□ 必要ならユーザーに質問した
□ research.mdを作成した
□ plan.mdを更新した
□ 「？」が0個になった

## Step 6: Phase 1
□ data-model.mdを作成した
□ 全エンティティを詳細化した
□ contracts/を作成した (API機能の場合)
□ 全エンドポイントを定義した
□ quickstart.mdを作成した
□ セットアップ手順を記載した

## Step 7: Phase 1後チェック
□ 憲章チェックを実行した
□ data-model.mdが憲章に準拠
□ contracts/が憲章に準拠
□ quickstart.mdにテスト手順あり
□ 違反がなければPhase 4へ

## Phase 3完了
□ 全ての設計書が完成
□ 憲章に準拠
□ 実装可能な状態
```

---

### 用語集

```
Technical Context:
  実装に必要な技術的情報をまとめたセクション

NEEDS CLARIFICATION:
  調査が必要な項目を示すマーク

Phase -1:
  Phase 0より前の憲章チェック

research.md:
  技術調査の過程と理由を記録するファイル

data-model.md:
  データベース設計を詳細に記述するファイル

contracts/:
  API仕様をOpenAPI形式で定義するディレクトリ

quickstart.md:
  新しい開発者のためのセットアップガイド

憲章 (constitution):
  プロジェクト全体で守るべきルールと制約

エンティティ:
  データベースで管理するオブジェクト (User, Messageなど)

API契約:
  クライアントとサーバー間の通信仕様の約束

テストカバレッジ:
  テストで実行されたコードの割合
```

---

**ドキュメント作成日**: 2025-11-18  
**バージョン**: 1.0  
**次回更新予定**: Phase 4学習完了後
