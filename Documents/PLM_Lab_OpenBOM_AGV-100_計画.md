# PLM Hands-on Lab 計画書

## OpenBOMを用いたAGV-100製品管理デモ

- **対象製品**：AGV-100 小型無人搬送車
- **対象PLM**：OpenBOM
- **検証期間**：Day 1～Day 10
- **1日あたりの目安**：60～90分
- **本書の位置づけ**：PLM学習・製品比較・デモ環境構築の共通計画

---

# 1. 取り組みの目的

本取り組みでは、無償または低コストで利用できるPLM検証環境を用意し、共通のデモ製品・デモデータ・デモシナリオを使って、PLMの製品知識と実務的な使い方を習得する。

単に文書をPLMへ登録するのではなく、製品、部品、要求、設計諸元、BOM、原価、サプライヤ、変更情報を構造化し、製品を中心に情報を関連付けることを重視する。

## 1.1 到達目標

1. PLMにおけるProduct、Part、BOM、Requirement、Changeの考え方を理解する。
2. AGV-100の製品モデルをPLM上に構築する。
3. EBOMと部品原価から製品原価を算出する。
4. 要求変更から部品、BOM、図面、原価への影響を追跡する。
5. OpenBOMの強み・制約を13の評価項目で確認する。
6. Aras Innovatorなど、次に検証するPLMとの比較基準を作る。

---

# 2. 基本方針

## 2.1 製品管理を主軸とする

最初の検証範囲は設備管理ではなく、製品ライフサイクル管理とする。

```text
Requirement
    ↓
Product / Part
    ↓
Design Parameter
    ↓
EBOM
    ↓
Cost / Supplier
    ↓
ECR / ECO
    ↓
Revision / Baseline
```

設備、個体、As-Built、As-Maintained、保全管理は、製品管理を理解した後の次期フェーズで扱う。

## 2.2 Document中心ではなくProduct中心とする

本Labでは、Word、Excel、PDFをPLMへ保管するだけの運用を目指さない。

```text
                  AGV-100
                     │
      ┌──────────────┼──────────────┐
      │              │              │
 Requirement      EBOM           Cost
      │              │              │
 Design Parameter   Part       Supplier / Quote
      │              │              │
      └────────── Change ────────────┘
                     │
                  Document
```

AGV-100を起点として、要求、設計諸元、構成、原価、サプライヤ、変更、文書へ辿れる状態を目標とする。

## 2.3 構造化できる情報はPLMを正本とする

| 情報 | 基本的な正本 | 補足 |
|---|---|---|
| Product ID / Part Number | PLM | Wordへの重複入力を避ける |
| 製品・部品属性 | PLM | 分類、重量、電圧、Make/Buyなど |
| Requirement | PLM | 必要に応じて帳票出力する |
| 設計諸元 | PLM | 数値、単位、許容範囲を構造化する |
| EBOM | PLM | WordやExcelを正本にしない |
| Target Cost / Design Cost | PLM | 製品属性またはCost Tableで管理する |
| Supplier / Sourcing | PLMまたはERP/SRM | System of Recordを明確にする |
| 見積原本 | ファイル | PLMのSupplier、Part、Quoteに関連付ける |
| 実購入価格・実績原価 | ERP | PLMから参照する構成を基本仮説とする |
| 図面・CAD | CADファイル＋PLM | PLMで版・関連・状態を管理する |
| ECR / ECO | PLM | 必要に応じて帳票出力する |

## 2.4 Wordに残す情報

Word形式の製品仕様書や部品仕様書は、次のような文章・説明・外部提出用情報を中心とする。

- 製品の目的、背景、コンセプト
- 想定用途、使用環境、制約条件
- 詳細な補足説明、注意事項
- 文書として承認・提出する必要がある要求
- PLM上のProduct、Requirement、EBOM等への参照

構成品一覧、詳細EBOM、個別購入価格、原価内訳は原則としてWordに重複保持しない。

---

# 3. デモ製品 AGV-100

## 3.1 製品概要

AGV-100は、屋内工場で部品・仕掛品を搬送する小型無人搬送車とする。機械、電気、制御ソフトウェアを含む複合製品とし、PLMの基本的な製品管理を一通り検証できる構成とする。

## 3.2 初期製品構成

```text
AGV-100
├─ ME-100 Mechanical Unit × 1
├─ DU-100 Drive Unit × 2
│  ├─ MT-100 Drive Motor × 1
│  └─ GR-100 Gear Unit × 1
├─ EL-100 Electrical Unit × 1
│  └─ BT-100 Battery × 1
└─ CS-100 Control System × 1
```

## 3.3 Rev.Aの主要条件

| 項目 | Rev.A |
|---|---:|
| 最大積載量 | 500 kg |
| 最大速度 | 60 m/min |
| 連続稼働時間 | 8 h |
| バッテリー電圧 | 48 V |
| Motor | MT-100 |
| Motor定格出力 | 1.0 kW |
| Motor参考単価 | 30,000円／台 |
| Target Cost | 300,000円／製品 |

## 3.4 変更シナリオ

顧客要求により、最大積載量を500 kgから700 kgへ変更する。

```text
REQ-001変更
最大積載量 500 kg → 700 kg
        ↓
ECR-001
        ↓
ECO-001
        ↓
MT-100 → MT-200
GR-100 → GR-200
        ↓
EBOM Rev.A → Rev.B
        ↓
製品原価を再計算
        ↓
影響対象の確認・再検証
```

## 3.5 Rev.Bの想定変更

| 項目 | Rev.A | Rev.B |
|---|---:|---:|
| 最大積載量 | 500 kg | 700 kg |
| Motor | MT-100 | MT-200 |
| Motor出力 | 1.0 kW | 1.5 kW |
| Motor単価 | 30,000円 | 38,000円 |
| Motor使用数量 | 2台 | 2台 |
| Motor原価 | 60,000円 | 76,000円 |
| Motor原価差額 | － | +16,000円／製品 |

---

# 4. PLM製品の評価項目

## 4.1 中核となる13項目

### A. Product Model

1. **Product / Partをオブジェクトとして管理できるか**
2. **属性を自由に定義できるか**
3. **Requirementを構造化できるか**
4. **EBOMを管理できるか**
5. **設計諸元を構造化できるか**

### B. Cost / Sourcing

6. **Cost Tableを持てるか**
7. **BOM Cost Roll-upできるか**
8. **Supplier / Quoteを関連付けられるか**

### C. Configuration / Change

9. **ECR / ECOで一括変更できるか**
10. **RevisionごとにBaselineを比較できるか**
11. **文書ではなくProductを中心に情報を辿れるか**

### D. PLM Platform

12. **Relationshipを自由に定義できるか**
13. **Impact Analysisができるか**

## 4.2 評価基準

各項目を5段階で評価する。

| 点数 | 評価基準 |
|---:|---|
| 5 | 標準機能で容易に実現でき、実務適用も期待できる |
| 4 | 標準機能で実現できるが、一定の設定が必要 |
| 3 | 工夫や代替表現によって実現できる |
| 2 | 一部のみ実現可能で、制約が大きい |
| 1 | 標準機能では困難、または外部ツールが必要 |
| 0 | 検証できなかった |

## 4.3 操作性の別評価

機能点とは別に、以下を毎日5段階で記録する。

- 環境準備の容易さ
- 初見での分かりやすさ
- 操作ステップ数
- データ登録のしやすさ
- 設定変更の容易さ
- エラー・詰まりポイント
- ヘルプや学習情報の探しやすさ

単純な機能合計だけで製品を選ばず、**実現可能性と使いやすさの両方**を評価する。

---

# 5. OpenBOMの想定評価

| # | 評価項目 | 事前評価 | 検証する内容 |
|---:|---|:---:|---|
| 1 | Product / Part | ◎ | Catalog Item、BOM、Custom Object |
| 2 | 属性の自由定義 | ◎ | Custom Property、型、単位、表示 |
| 3 | Requirement | ○ | Custom Objectとして管理できるか |
| 4 | EBOM | ◎ | 多階層BOM、数量、Where Used |
| 5 | 設計諸元 | ○ | Product PropertyまたはCustom Object |
| 6 | Cost Table | ◎ | Target、Purchase、Design Cost |
| 7 | BOM Cost Roll-up | ◎ | Formula、単階層・多階層集計 |
| 8 | Supplier / Quote | ◎ | Vendor、Sourcing、Quote文書 |
| 9 | ECR / ECO | ○ | Change Request / Change Order、承認 |
| 10 | Revision / Baseline | ◎ | Revision、履歴、BOM比較 |
| 11 | Product中心参照 | ○ | Item、BOM、Document、Changeの関連 |
| 12 | Relationship | ○ | Object Referenceの自由度 |
| 13 | Impact Analysis | △ | Where Used、Compare、Referenceでの追跡 |

## 5.1 OpenBOMに期待する強み

- SaaSで短時間に開始できる
- Product / Part属性を柔軟に追加できる
- 多階層EBOMを扱える
- 数式とRoll-upにより原価を計算できる
- Supplier、Sourcing、Quoteとの関係を作れる
- RevisionとBOM差分を確認できる
- 無料Trialで短期検証できる

## 5.2 特に確認すべき制約

1. RequirementをProduct Modelの一部として十分に扱えるか
2. Requirement、Design Parameter、Part間の関係を自然に表現できるか
3. BOM以外のObjectを含むBaselineを作れるか
4. 変更時に影響対象を横断的に追跡できるか
5. Company相当の機能がTrialでどこまで利用できるか
6. OpenBOMを原価・調達情報の正本とする範囲が適切か

---

# 6. Day 1～Day 10 実施計画

## Day 1：環境準備とデータモデル設計

### 目的

OpenBOMの操作を始める前に、AGV-100で管理するObject、Attribute、Relationshipを定義する。

### 実施内容

1. OpenBOM Trial環境を用意する。
2. 管理対象Objectを整理する。
   - Product
   - Part
   - Requirement
   - Design Parameter
   - Supplier
   - Quote
   - Document
   - Change
3. 各Objectの主要属性を定義する。
4. Relationshipを定義する。
5. PLMとERPのSystem of Record境界を仮決めする。

### 成果物

- AGV-100 PLMデータモデル v1
- Object一覧
- Attribute一覧
- Relationship一覧
- System of Record整理表

### 完了条件

- 何をWordで持ち、何をPLMで構造化するか説明できる。
- AGV-100を起点とした情報モデルが描けている。

---

## Day 2：Product / Part登録

### 目的

AGV-100および主要構成品を再利用可能なItemとして登録する。

### 実施内容

1. Catalogを作成する。
2. AGV-100、主要Unit、主要Partを登録する。
3. 共通Propertyを追加する。
   - Part Number
   - Name
   - Description
   - Type
   - Category
   - Make / Buy
   - Lifecycle State
   - Weight
4. Motor固有Propertyを追加する。
   - Rated Power
   - Rated Voltage
   - Rated Torque
   - Supplier
   - Lead Time

### 成果物

- AGV Product Catalog
- Part Master
- Property定義一覧

### 完了条件

- ProductとPartを独立したItemとして検索・表示できる。
- Word文書なしでも主要属性を確認できる。

---

## Day 3：EBOM作成

### 目的

AGV-100の多階層設計BOMを構築し、親子関係と数量を確認する。

### 実施内容

1. AGV-100を最上位とするEBOMを作成する。
2. ME-100、DU-100、EL-100、CS-100を追加する。
3. DU-100配下にMT-100、GR-100を追加する。
4. 数量を設定する。
5. Multi-level表示を確認する。
6. Flattened BOMを確認する。
7. MT-100のWhere Usedを確認する。

### 成果物

- AGV-100 EBOM Working版
- EBOM階層図
- Where Used確認結果

### 完了条件

- AGV-100から下位部品へ辿れる。
- MT-100からDU-100、AGV-100へ逆方向に辿れる。

---

## Day 4：RequirementとDesign Parameter

### 目的

製品要求と設計諸元をWord本文ではなく、構造化データとして管理する。

### Requirement例

| Req ID | 要求 | 判定基準 |
|---|---|---|
| REQ-001 | 最大積載量 | 500 kg以上 |
| REQ-002 | 最大速度 | 60 m/min以上 |
| REQ-003 | 連続稼働時間 | 8 h以上 |
| REQ-004 | 障害物検知 | 安全停止できること |

### Design Parameter例

| Parameter ID | 設計諸元 | 値 | 単位 |
|---|---|---:|---|
| DP-001 | Max Payload | 500 | kg |
| DP-002 | Max Speed | 60 | m/min |
| DP-003 | Operating Time | 8 | h |
| DP-004 | Battery Voltage | 48 | V |

### 実施内容

1. Requirementの管理方法を決める。
2. Requirementを登録する。
3. Design Parameterの管理方法を決める。
4. Design Parameterを登録する。
5. Requirement、Parameter、AGV-100を関連付ける。

### 成果物

- Requirement一覧
- Design Parameter一覧
- Requirement → Parameter → Product関係

### 完了条件

- REQ-001とDP-001の関係を説明できる。
- Requirementを変更した際の影響先を確認できる。

---

## Day 5：Cost Table作成

### 目的

目標原価と設計原価をPLM上の構造化データとして管理する。

### 登録する原価タイプ

- Target Cost
- Purchase Cost
- Material Cost
- Process Cost
- Design Cost
- Currency
- Cost Source
- Effective Date

### 実施内容

1. AGV-100にTarget Costを設定する。
2. 購入品にPurchase Costを設定する。
3. 内製品にMaterial Cost、Process Costを設定する。
4. Currency、Cost Source、Effective Dateを設定する。
5. PLMとERPの原価責任範囲を確認する。

### 成果物

- AGV-100 Cost Table
- Part Cost一覧
- PLM / ERP原価境界表

### 完了条件

- Target Costと実際の積上げ対象原価を区別できる。
- Wordを開かずに製品・部品の設計原価を確認できる。

---

## Day 6：BOM Cost Roll-up

### 目的

EBOMの数量と部品原価を掛け合わせ、製品原価を積み上げる。

### 計算例

```text
Extended Cost = Unit Cost × Quantity

MT-100  30,000円 × 2 = 60,000円
GR-100  15,000円 × 2 = 30,000円
BT-100  50,000円 × 1 = 50,000円
```

### 実施内容

1. Unit Cost Propertyを設定する。
2. Extended Cost Formulaを設定する。
3. 下位部品からAssemblyへRoll-upする。
4. AGV-100のDesign Costを算出する。
5. Target Costとの差を算出する。

### 成果物

- AGV-100 Costed EBOM
- Target Cost対比
- 原価差異一覧

### 完了条件

- 多階層BOMから製品原価を算出できる。
- 価格または数量変更時に製品原価が更新される。

---

## Day 7：Supplier / Quote関連付け

### 目的

Part、Supplier、Quote、Costの関係を構築する。

### 実施内容

1. DEMO Motor Co.をSupplierとして登録する。
2. MT-100とSupplierを関連付ける。
3. Unit Price、MOQ、Lead Timeを登録する。
4. 見積書QUO-2026-001を関連付ける。
5. Quote PriceとCost Tableの値を照合する。
6. 価格正本をPLMまたはERPのどちらに置くか検討する。

### 成果物

- Supplier一覧
- Part → Supplier → Quote → Cost関係
- Sourcing情報
- System of Record判断メモ

### 完了条件

- MT-100からSupplierと見積書へ辿れる。
- 見積書が証跡、価格属性が構造化データであることを説明できる。

---

## Day 8：Revision AとBaseline

### 目的

変更前の正式構成をRev.Aとして凍結する。

### Rev.A対象

- Product属性
- Requirement
- Design Parameter
- EBOM
- Part属性
- Cost
- 関連Document

### 実施内容

1. 登録データの整合性を確認する。
2. Working状態からReleased状態へ移す。
3. AGV-100 Rev.Aを作成する。
4. Rev.AのBOM、属性、添付文書を確認する。
5. RevisionとBackupの違いを整理する。

### 成果物

- AGV-100 Rev.A Baseline
- Rev.A確認チェックリスト

### 完了条件

- Rev.Aが変更不能な正式スナップショットとして参照できる。
- Rev.A時点のBOMと原価を再現できる。

---

## Day 9：ECR / ECOによる変更

### 目的

要求変更を起点に、影響分析と変更処理を実行する。

### 変更要求

```text
最大積載量
500 kg → 700 kg
```

### 実施内容

1. ECR-001を作成する。
2. 変更理由、優先度、要求元を登録する。
3. 影響対象を抽出する。
   - REQ-001
   - DP-001
   - DU-100
   - MT-100
   - GR-100
   - EBOM
   - Cost
   - Drawing
   - Test
4. ECO-001を作成する。
5. MT-100をMT-200へ置換する。
6. GR-100をGR-200へ置換する。
7. 承認プロセスを実行または模擬する。

### 成果物

- ECR-001
- ECO-001
- Affected Item一覧
- 影響分析メモ

### 完了条件

- 変更要求と変更実施を分離して管理できる。
- 変更対象と変更理由を追跡できる。

---

## Day 10：Rev.B、差分比較、Impact Analysis

### 目的

変更後構成をRev.Bとして確定し、Rev.Aとの差分と影響範囲を確認する。

### 実施内容

1. Requirementを700 kgへ変更する。
2. Design Parameterを700 kgへ変更する。
3. EBOMへMT-200、GR-200を反映する。
4. Cost Roll-upを再実行する。
5. AGV-100 Rev.Bを作成する。
6. Rev.AとRev.BのBOM差分を比較する。
7. Cost差分を比較する。
8. Where UsedとObject Referenceで影響範囲を確認する。
9. 13項目を採点する。
10. OpenBOMの強み、制約、次の検証課題をまとめる。

### 成果物

- AGV-100 Rev.B Baseline
- Rev.A / Rev.B Change Report
- Cost差額レポート
- Impact Analysis結果
- OpenBOM評価表

### 完了条件

- Requirement変更からPart、EBOM、Cost、Document、Testまで辿れる。
- OpenBOMの13項目評価を記録できる。
- Aras Innovatorで追加確認すべき項目を説明できる。

---

# 7. 日次検証記録テンプレート

各Dayで以下を記録する。

```markdown
## Day X 実施記録

### 実施日
YYYY/MM/DD

### 実施時間
XX分

### 実施内容
- 
- 

### 作成・変更したObject
- 

### 確認できたこと
- 

### 制約・できなかったこと
- 

### エラー・詰まりポイント
- 

### 操作性評価
| 項目 | 点数 | コメント |
|---|---:|---|
| 分かりやすさ | /5 | |
| 操作の少なさ | /5 | |
| 設定の容易さ | /5 | |
| データ登録の容易さ | /5 | |
| ヘルプの充実度 | /5 | |

### 次回への持越し
- 
```

---

# 8. 最終評価表

| # | 評価項目 | 点数 | 確認結果・根拠 |
|---:|---|---:|---|
| 1 | Product / Part | /5 | |
| 2 | Attributes | /5 | |
| 3 | Requirement | /5 | |
| 4 | EBOM | /5 | |
| 5 | Design Parameter | /5 | |
| 6 | Cost Table | /5 | |
| 7 | BOM Cost Roll-up | /5 | |
| 8 | Supplier / Quote | /5 | |
| 9 | ECR / ECO | /5 | |
| 10 | Revision / Baseline | /5 | |
| 11 | Product-centric Traceability | /5 | |
| 12 | Relationship Model | /5 | |
| 13 | Impact Analysis | /5 | |
|  | **合計** | **/65** | |

## 8.1 最終判断

| 判断項目 | 結果 |
|---|---|
| PLM学習用として適しているか | |
| デモ環境を容易に再現できるか | |
| 製品BOM・原価管理に適しているか | |
| Requirement管理に適しているか | |
| Change / Impact Analysisに適しているか | |
| 実案件PoCへ発展できるか | |
| 次に比較すべきPLM | Aras Innovatorほか |

---

# 9. 今回使用するデータ・文書

## 9.1 構造化データ

- Product Master
- Part Master
- Requirement一覧
- Design Parameter一覧
- EBOM Rev.A / Rev.B
- Cost Table
- Supplier / Sourcing情報
- Revision / Baseline
- ECR-001 / ECO-001

## 9.2 関連文書

- AGV-100製品仕様書
- MT-100ドライブモータ部品仕様書
- MT-200高出力ドライブモータ部品仕様書
- MT-100見積書
- MT-200見積書
- AGV-100総組立図
- Drive Unit組立図
- 必要に応じた試験・検証証跡

## 9.3 文書の扱い

文書は情報の正本を二重化するためではなく、次の用途でPLMに関連付ける。

- 詳細説明
- 外部受領証跡
- 図面・CAD原本
- 承認済み帳票
- 試験・検査証跡
- 契約・見積の原本

---

# 10. 10日後の完成イメージ

```text
AGV-100 Rev.B
│
├─ Requirements
│  ├─ REQ-001 Max Payload >= 700 kg
│  ├─ REQ-002 Max Speed >= 60 m/min
│  └─ REQ-003 Operating Time >= 8 h
│
├─ Design Parameters
│  ├─ Max Payload = 700 kg
│  ├─ Max Speed = 60 m/min
│  └─ Battery Voltage = 48 V
│
├─ EBOM
│  ├─ DU-100 × 2
│  │  ├─ MT-200
│  │  └─ GR-200
│  ├─ EL-100
│  │  └─ BT-100
│  └─ CS-100
│
├─ Cost
│  ├─ Target Cost
│  ├─ Design Cost
│  └─ BOM Cost Roll-up
│
├─ Supplier / Quote
│  └─ MT-200
│     ├─ DEMO Motor Co.
│     └─ QUO-2026-004
│
├─ Change
│  ├─ ECR-001
│  └─ ECO-001
│
├─ Baseline
│  ├─ Rev.A
│  └─ Rev.B
│
└─ Documents
   ├─ Product Specification
   ├─ Part Specification
   ├─ Drawing
   ├─ Supplier Quote
   └─ Test Evidence
```

本Labの最終目的は、このProduct ModelをOpenBOM上で構築し、**AGV-100を起点として要求、諸元、EBOM、原価、サプライヤ、変更、Revision、文書へ辿れる状態を実現すること**である。
