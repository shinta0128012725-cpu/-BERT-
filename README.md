# 📰 日本語ニュース分類 with BERT ファインチューニング

日本語ニュース記事を9カテゴリに分類する自然言語処理プロジェクト。  
東北大学公開の日本語BERTモデル（`cl-tohoku/bert-base-japanese-whole-word-masking`）をファインチューニングして構築した。

---

## 🎯 プロジェクトの位置づけ

NLPタスクにおける現在の主流アプローチである**事前学習済みモデルの転移学習・ファインチューニング**を実践することが本プロジェクトの目的。



---

## 📊 データ

- **データ形式:** カテゴリ別フォルダに格納されたテキストファイル（`.txt`）
- **カテゴリ数:** 9クラス
- **前処理:** 改行・タブ・全角スペースの除去
- **分割:** train / test にランダム分割してCSV保存

---

## ⚙️ モデルと実装

### 使用モデル

| 項目 | 内容 |
|------|------|
| ベースモデル | `cl-tohoku/bert-base-japanese-whole-word-masking` |
| タスク | シーケンス分類（`BertForSequenceClassification`） |
| 出力クラス数 | 9 |
| トークナイザー | `BertJapaneseTokenizer`（fugashi + ipadic） |
| 実行環境 | Google Colab（GPU: CUDA） |

### トークン長の事前確認

ファインチューニング前にトレーニングデータ全体のトークン数を集計し、`max_length` を決定した。

```
最大トークン数・平均・中央値・95パーセンタイルを確認 → max_length=256 に設定
```

95パーセンタイルを確認することで、外れ値の影響を避けながら情報の欠損を最小限に抑えるトークン長を設定している。

### ハイパーパラメータ

| パラメータ | 値 |
|------------|---|
| エポック数 | 2 |
| 訓練バッチサイズ | 8 |
| 評価バッチサイズ | 32 |
| Warmupステップ | 500 |
| Weight Decay | 0.01 |
| 評価タイミング | steps |

---

## 🔁 パイプライン

```
テキストファイル（カテゴリ別フォルダ）
  └─ 前処理（改行・タブ・全角スペース除去）
  └─ ラベル付与（フォルダインデックス）
  └─ train / test 分割 → CSV保存
  └─ トークナイズ（BertJapaneseTokenizer, max_length=256）
  └─ ファインチューニング（Trainer API）
  └─ モデル保存 → 推論
```

---

## 🛠️ 使用技術・ライブラリ

| カテゴリ | ライブラリ |
|----------|-----------|
| モデル・トークナイザー | Hugging Face Transformers, `cl-tohoku/bert-base-japanese-whole-word-masking` |
| 日本語形態素解析 | fugashi, ipadic |
| データ処理 | Hugging Face datasets, pandas |
| 学習 | Hugging Face Trainer API |
| 評価 | scikit-learn（accuracy_score） |
| 実行環境 | Google Colab, PyTorch（CUDA） |

---

## 📁 ファイル構成

```
.
├── BERTニュース分類.ipynb     # メインノートブック
├── news_train.csv             # 訓練データ（生成）
├── news_test.csv              # テストデータ（生成）
└── README.md
```

---

## 🔗 関連プロジェクト

- **[フェイクニュース判別（CountVectorizer + XGBoost）](#)** — カウントベース・ルールベース手法による古典的NLPアプローチ
