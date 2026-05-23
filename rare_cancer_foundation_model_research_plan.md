# 希少がん個人リサーチにおける基盤モデル活用計画

## 1. 基本方針

希少がんに対して有効な治療選択肢が限られる場合、自分の治療歴、病理情報、ゲノム情報、検査値、症状ログ、論文、治験情報、薬剤情報を整理し、主治医や専門医に相談するための仮説を作る。

ただし、この取り組みは医療判断そのものではなく、以下に限定する。

- 自分の情報整理
- 論文・治験・薬剤情報の収集
- 標的分子・変異・経路に関する仮説整理
- 医師に相談する質問の生成
- セカンドオピニオンや分子腫瘍ボードに持ち込む材料作成

避けるべきことは以下。

- 自分で治療方針を決める
- 未承認薬やサプリメントを自己判断で使用する
- AIの出力を診断・処方・投薬調整として扱う
- 東洋医学・補完療法を標準治療の代替として扱う
- 患者データを外部SaaSに安易にアップロードする

---

## 2. 目指すツール像

## Personal Rare Cancer Evidence Navigator

### コンセプト

希少がん患者本人が、自分の治療歴・バイオデータ・ゲノム情報と、論文・治験・薬剤・標的分子情報を対応付け、治療仮説を整理する個人用リサーチ支援ツール。

### 目的

- 自分の病歴と検査情報を時系列で整理する
- がん種名だけでなく、遺伝子変異・融合遺伝子・発現・経路から情報を探す
- 論文、症例報告、治験、ガイドライン、薬剤情報を構造化する
- 候補治療、治験、適応外使用、支持療法、補完療法を区別して管理する
- 主治医に聞くべき質問を生成する

---

## 3. AlphaFold型モデルを使う位置づけ

AlphaFoldのような構造予測モデルは、直接「治療を決める」ものではない。

現実的な使い方は以下。

- 変異タンパク質の構造変化の仮説を見る
- 変異が薬剤結合部位、タンパク質間相互作用、活性部位に近いかを見る
- 既知薬剤や候補分子との結合可能性を考える前処理に使う
- 論文中の標的分子・変異・薬剤の関係を構造的に理解する
- 研究者・医師に相談するための可視化資料を作る

重要なのは、構造予測は仮説生成であり、臨床有効性の証明ではないこと。

---

## 4. 活用候補モデル

### AlphaFold 3

用途:

- タンパク質、核酸、小分子、イオンなどを含む生体分子複合体の構造予測
- 変異タンパク質と候補分子の相互作用仮説
- タンパク質間相互作用の理解

注意:

- AlphaFold Serverは非商用利用向け
- 予測構造は実験結果ではない
- 薬効、安全性、投与量、臨床効果は判断できない

参考:

- https://www.nature.com/articles/s41586-024-07487-w
- https://www.isomorphiclabs.com/articles/alphafold-3-predicts-the-structure-and-interactions-of-all-of-lifes-molecules
- https://www.ebi.ac.uk/training/online/courses/alphafold/alphafold-3-and-alphafold-server/alphafold-server-your-gateway-to-alphafold-3/

### ESM / ESM3

用途:

- タンパク質配列、構造、機能の表現学習
- 変異の影響予測の補助
- タンパク質機能や進化的制約の理解
- タンパク質設計や候補配列生成の研究

注意:

- 生成結果を治療に直結させない
- 個人で扱うなら、まずは既存モデルの推論・可視化・文献理解用途が現実的

参考:

- https://github.com/evolutionaryscale/esm
- https://www.evolutionaryscale.ai/

### Boltz-1

用途:

- AlphaFold 3に近い方向のオープンな生体分子相互作用モデリング
- タンパク質・リガンド・複合体構造の仮説生成
- ローカル実験や研究ワークフローへの組み込み候補

注意:

- 予測精度や適用範囲はケースごとに検証が必要
- 臨床判断ではなく研究仮説生成として扱う

参考:

- https://boltz.bio/boltz1
- https://pmc.ncbi.nlm.nih.gov/articles/PMC11601547/

---

## 5. 個人リサーチでの入力データ

### 医療情報

- 正式診断名
- 組織型
- 原発部位
- ステージ
- 再発・転移部位
- 病理レポート
- 画像検査の所見
- 治療歴
- 効果判定
- 副作用
- 現在の全身状態

### 分子情報

- 遺伝子変異
- 融合遺伝子
- コピー数変化
- MSI
- TMB
- PD-L1
- HER2
- NTRK
- BRAF
- ALK
- ROS1
- RET
- MET
- EGFR
- KRAS
- NRAS
- その他パネル検査結果

### 生活・バイオログ

- 体重
- 食欲
- 睡眠
- 疼痛
- 倦怠感
- 血液検査値
- 服薬
- サプリメント
- 漢方・補完療法
- 運動量
- 主観症状

---

## 6. 出力する候補仮説

```yaml
candidate:
  category: clinical_trial | approved_drug | off_label | biomarker_hypothesis | structure_hypothesis | supportive_care | integrative_care
  title: ""
  target:
    gene: ""
    protein: ""
    pathway: ""
    mutation: ""
  patient_match:
    cancer_type_match: high | medium | low | unknown
    biomarker_match: high | medium | low | unknown
    prior_treatment_match: high | medium | low | unknown
    eligibility_concerns:
      - ""
  evidence:
    level: guideline | phase3 | phase2 | phase1 | retrospective | case_report | preclinical | computational
    sources:
      - ""
  foundation_model_analysis:
    model: AlphaFold3 | ESM | Boltz1 | other
    purpose: ""
    result_summary: ""
    confidence_limits:
      - ""
  risks:
    known_toxicity:
      - ""
    interactions:
      - ""
    uncertainty:
      - ""
  questions_for_doctor:
    - ""
```

---

## 7. エビデンスの扱い

治療候補は、必ずエビデンスレベルで分ける。

高い順の目安:

1. ガイドライン・標準治療
2. 第3相試験
3. 第2相試験
4. 第1相試験
5. 後ろ向き研究
6. 症例報告
7. 前臨床研究
8. 計算予測・構造予測
9. 体験談・商業的宣伝

AlphaFold、ESM、Boltzなどの出力は、原則として「8. 計算予測・構造予測」に置く。

---

## 8. 東洋医学・補完療法を含める場合の扱い

東洋医学、漢方、サプリメント、食事療法、代替療法は、標準治療の代替ではなく、支持療法・症状緩和・QOL改善の候補として扱う。

評価する観点:

- 症状緩和のエビデンス
- 抗がん剤との相互作用
- 免疫療法との相互作用
- 肝機能・腎機能への影響
- 出血リスク
- 手術・放射線治療との関係
- 医師・薬剤師に確認すべき点

避ける表現:

- がんが治る
- 標準治療より有効
- 副作用がない
- 自然だから安全

---

## 9. MVPロードマップ

### Phase 1: 個人データ整理

- MarkdownまたはSQLiteで病歴タイムラインを作る
- 検査値、治療歴、病理、ゲノム検査結果を構造化する
- 主治医に確認したい質問リストを作る

### Phase 2: 論文・治験検索

- PubMed検索クエリを保存する
- ClinicalTrials.gov、jRCT、NCI、国立がん研究センターの検索結果を整理する
- がん種名だけでなく、遺伝子変異・標的分子・経路で検索する

### Phase 3: LLMによる文献構造化

- 論文abstractから以下を抽出する
  - がん種
  - 標的
  - 薬剤
  - バイオマーカー
  - 試験相
  - 対象患者
  - 結果
  - 限界

### Phase 4: 構造モデル活用

- 変異タンパク質のUniProt IDを特定する
- 変異位置をタンパク質構造上にマッピングする
- AlphaFold 3、ESM、Boltzなどで構造・相互作用仮説を生成する
- 予測結果を医師に見せるための説明資料に変換する

### Phase 5: 医師相談用レポート

- 候補治療・治験・論文をエビデンスレベル順に整理する
- 自分との一致度を明示する
- 不確実性とリスクを明示する
- 主治医への質問として出力する

---

## 10. 最初に作るもの

最初のMVPは以下。

> 自分の診断名、治療歴、遺伝子変異、検査値を入力し、関連論文・治験・標的分子・候補薬剤を整理して、主治医に相談する質問リストを生成するツール。

AlphaFold型モデルは、最初から中核にしない。

まずは以下を中核にする。

- 病歴タイムライン
- ゲノム検査結果の構造化
- 論文・治験検索
- エビデンスレベル分類
- 医師相談用レポート

その後、構造予測モデルを以下のように追加する。

```text
病歴・ゲノム情報整理
  → 論文・治験・薬剤情報の構造化
  → 標的分子・変異・経路の整理
  → AlphaFold/ESM/Boltzによる構造仮説
  → 医師相談用レポート
```

