# 学校教育向けテスト機能 DB設計書（テンプレート）

## 1. ドキュメント情報

| 項目      | 内容                |
|---------|-------------------|
| ドキュメント名 | 学校教育向けテスト機能 DB設計書 |
| バージョン   | 1.0               |
| 作成日     | 2025-09-02        |
| 作成者     | （作成者名）            |
| 承認者     | （承認者名）            |

---

## 2. 目的・スコープ

- 目的：学校教育向けテスト機能のデータベース構造を定義し、開発・運用間の共通認識を形成する。
- スコープ：
  - 対象：生徒・教員・クラス・科目・テスト・設問・配信・受験・回答・採点・成績に関するデータ
  - 対象外：学習動画、保護者ポータル、課金/請求

---

## 3. 用語と表記規約

- 命名規則：
  - テーブル名：snake_case 複数形（例：students, test_items）
  - カラム名：snake_case（例：student_id, created_at）
  - 主キー：id（UUID v7 または BIGINT いずれかの方針を採用）
  - 外部キー：参照先名 + _id
  - 監査系カラム：created_at, created_by, updated_at, updated_by
  - ソフトデリート：deleted_at（NULL で有効）
- データ型の指針（例：MySQL）：
  - ID：BINARY(16) または BIGINT UNSIGNED
  - 文字列：VARCHAR(255)／長文：TEXT
  - 真偽値：TINYINT(1) または BOOLEAN
  - 日時：TIMESTAMP(3) または DATETIME(3)
  - JSON型：JSON
- 文字コード/照合順序：utf8mb4 / utf8mb4_unicode_ci
- タイムゾーン：DBはUTC固定。表示時にアプリ側でローカライズ。

---

## 4. 全体構成（エンティティ一覧）

- 組織系：schools, classes, teachers, students, enrollments
- 学習系：subjects, courses, course_classes（任意）
- テスト系：tests, test_sections, test_items, test_item_choices
- 配信/受験系：test_assignments, test_sessions
- 回答/採点系：answers, scoring_results, grades
- 補助：attachments, accommodations, audit_logs, idempotency_keys
- 参照データ：code_sets（ENUM代替のコードマスタ）

---

## 5. ER関係（文章記述）

- schools 1 — N classes
- classes N — N students（中間：enrollments）
- teachers 1 — N tests
- tests 1 — N test_sections 1 — N test_items 1 — N test_item_choices
- tests 1 — N test_assignments（配信先：クラスまたは個人）
- test_assignments 1 — N test_sessions（生徒ごとの受験）
- test_sessions 1 — N answers 1 — N scoring_results
- test_sessions 1 — 1 grades

---

## 6. 共通カラム・監査

- 監査：created_at, created_by, updated_at, updated_by
- 論理削除：deleted_at（必要に応じて採用）
- マルチテナント対応（任意）：tenant_id
- 監査ログ：audit_logs に操作種別・対象・変更差分を保存

---

## 7. テーブル定義（テンプレート）

### 7.x `<table_name>` テーブル

- 目的：テーブルの役割を記載
- 関係：関連テーブルと関係（1-N / N-1 / N-N）を記載
- 制約方針：主キー、ユニーク、外部キー、参照整合（ON UPDATE/DELETE）

**カラム定義**
| カラム名 | 型 | PK | NN | UQ | 既定値 | 説明 |
|---|---|:--:|:--:|:--:|---|---|

**インデックス**
| インデックス名 | 対象列 | ユニーク | 目的 |
|---|---|:--:|---|

**外部キー**
| FK名 | 子テーブル.列 | 親テーブル.列 | ON UPDATE | ON DELETE |
|---|---|---|---|---|

---

## 8. コアテーブル定義（例示的な見出しのみ）

### 8.1 schools

- 目的、関係、カラム定義、インデックス、外部キー

### 8.2 classes

- 目的、関係、カラム定義、インデックス、外部キー

### 8.3 students

- 目的、関係、カラム定義、インデックス、外部キー

### 8.4 enrollments（中間）

- 目的、関係、カラム定義、インデックス、外部キー、期間属性の方針

### 8.5 tests

- 目的、関係、カラム定義、インデックス、外部キー、メタ情報

### 8.6 test_items / test_item_choices

- 目的、関係、カラム定義、インデックス、外部キー、ユニークキー方針

### 8.7 test_assignments / test_sessions

- 目的、関係、カラム定義、インデックス、外部キー、再受験ポリシー

### 8.8 answers / scoring_results / grades

- 目的、関係、カラム定義、インデックス、外部キー、採点完了条件

---

## 9. 参照データ（コード）設計

- code_sets：code_type, code, label, sort_order, is_active 等
- 方針：変更頻度がある区分はENUMではなくテーブル化し、運用で更新可能にする。

---

## 10. インデックス最適化方針

- 外部キー列・頻出検索列（student_id, test_id, status, created_at）に索引付与
- レンジ検索（created_at）や複合絞り込み（test_id, created_at 等）を考慮
- レポート系にはカバリングインデックスを検討
- JOINキーの型・照合の整合を厳守

---

## 11. パーティション/アーカイブ方針

- 大量テーブル（answers, scoring_results, audit_logs）は期間パーティションを検討
- 古いデータをアーカイブ領域/別DBに退避する運用手順を定義

---

## 12. セキュリティ・プライバシ

- PIIの所在一覧（氏名、メール等）を明示し、最小権限でアクセス制御
- 行レベルアクセス（学校/クラス単位）の強制
- 監査ログで機微操作を追跡可能にする

---

## 13. データ整合性・トランザクション

- 受験→回答→採点→成績の整合性確保
- 採点完了条件：同一受験（session）内の全回答の採点完了をもって完了
- 成績集計は冪等性を担保（同一処理の重複防止）

---

## 14. マイグレーション方針

- 原則：後方互換を維持し、追加→移行→切替→削除の順で行う
- 影響評価：既存クエリ、インデックス再作成コスト、ロック時間、ロールバック手順

---

## 15. バックアップ/復旧

- 日次フル + ポイントインタイム復旧
- 四半期に一度のリストア演習
- 重要テーブルのエクスポート手順を整備

---

## 16. 最小実装セットと拡張

- 最小：schools, classes, students, enrollments, tests, test_items, test_item_choices, test_assignments, test_sessions,
  answers, scoring_results, grades
- 拡張：teachers, attachments, accommodations, audit_logs

---

## 17. 変更履歴

| 版   | 日付         | 変更者    | 変更内容 |
|-----|------------|--------|------|
| 1.0 | 2025-09-02 | （作成者名） | 初版作成 |
