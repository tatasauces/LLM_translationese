## Annotation: 
Annotation results along with the parsing and visualization scripts for three tasks with human annotations involved: translationese error span annotation for document-level translation (Figure 1), SFT translationese error span annotation (Figure 3), and translationese voting (Table 3).

## Evaluation: 
Translation results of different models and the corresponding evaluation scripts for Table 2 and Figure 5.

## TrainingData: 
Data for training models in Table 1, Figure 5 and Table 6.
---
## 結論

**這份 repo 確實包含與 GPT-4／ALMA 翻譯結果及「polished／潤飾後」資料相關的資料，但無法僅從檔名 100% 證明 `merge_pol` 全部都是 GPT-4 改寫資料。**

最重要的證據是：

- `Annotation/translationese_error_span/ana_annos.ipynb` 的分析結果明確列出：
  - `gpt-4-turbo_trans`
  - `gpt-4-turbo_end2end`
  - `gpt-4-turbo_ctx_pivot`
  - `alma_7B_trans`
  - `alma_13B_trans`
- 同一 notebook 的圖表標籤將模型分成：
  - `GPT-4`
  - `GPT-4-Specified`
  - `GPT-4-Polish`
  - `ALMA-7B`
  - `ALMA-13B`
- `TrainingData` 中有明確名為 `merge_pol.json` 與 `merge_pol_encsdeisru.json` 的訓練資料。
- `Evaluation/llama_results` 與 `Evaluation/qwen_results` 中也有對應 `merge_pol`、`merge_raw`、`merge_trans` 的模型輸出結果。

因此，**改寫／潤飾後資料應主要位於以下檔案：**

```text
TrainingData/
├── merge_sft_endezh/
│   └── merge_pol.json
└── merge_sft_encsdeisruzh/
    └── merge_pol_encsdeisru.json
```

其中：

- `merge_raw`：原始或未經潤飾的資料
- `merge_trans`：一般翻譯資料
- `merge_pol`：polished／潤飾後資料

但 repo 沒有提供資料字典或 README 說明 `merge_pol` 的完整產生流程，因此比較精確的說法是：

> Repo 包含標示為 `polished` 的改寫資料，且 annotation notebook 明確分析了 GPT-4 Polish 與 ALMA 產出的翻譯；不過沒有在資料檔內或文件中明確宣告 `merge_pol` 全部由 GPT-4 產生。

---

## What this is

這是一個以 Jupyter Notebook、人工標註資料、模型輸出與訓練資料組成的研究實作 repo，用於重現論文 **“Lost in Literalism: How Supervised Training Shapes Translationese in LLMs”** 的資料分析與實驗結果。

研究重點是比較不同模型及訓練資料形式對 translationese 的影響，特別是比較 ALMA、Mistral、GPT-3.5、GPT-4，以及翻譯、指定格式或潤飾後的輸出。

### Stack

- **Language(s):** Python／Jupyter Notebook
- **Framework / runtime:** Jupyter Notebook，Python 3.8 環境
- **Notable libraries:** PyTorch、Hugging Face Transformers、NLTK、Stanza、Pandas、NumPy、Matplotlib、Seaborn

---

## How it's organized

目前 `main` branch 的主要 tree 如下：

```text
Annotation/
├── training_instances_translationese_error_span/
│   ├── deen/
│   │   ├── project-34-*.json
│   │   ├── project-35-*.json
│   │   └── project-37-*.json
│   ├── enzh/
│   │   ├── project-47-*.json
│   │   ├── project-49-*.json
│   │   └── project-50-*.json
│   ├── pre_avg_esr_sft.ipynb
│   └── sft_tsr.pdf
│
├── translationese_error_span/
│   ├── deen/
│   │   └── task0/ ... task4/
│   ├── enzh/
│   │   └── task0/ ... task9/
│   ├── ana_annos.ipynb
│   └── enzh.tsr.pdf
│
└── translationese_vote/
    ├── ana_vote_enzh.ipynb
    ├── deen_vote.xlsx
    ├── enzh_vote.xlsx
    ├── vote_keys.json
    └── vote_keys_deen.json

Evaluation/
├── eval_heuristics.ipynb
├── eval_ppl.ipynb
├── llama_results/
│   └── LLaMA prediction outputs
└── qwen_results/
    └── Qwen prediction outputs

TrainingData/
├── filter/
│   ├── merge_raw.ppl_0.1.json
│   ├── merge_raw.ppl_0.2.json
│   ├── merge_raw.ppl_0.4.json
│   └── merge_raw.ppl_0.8.json
│
├── merge_sft_endezh/
│   ├── eval_deen.json
│   ├── eval_deen_wmt.json
│   ├── eval_enzh.json
│   ├── eval_enzh_wmt.json
│   ├── merge_raw.json
│   ├── merge_trans.json
│   └── merge_pol.json
│
└── merge_sft_encsdeisruzh/
    ├── eval_deen.json
    ├── eval_deen_wmt.json
    ├── eval_encs_wmt.json
    ├── eval_ende_wmt.json
    ├── eval_enis_wmt.json
    ├── eval_enru_wmt.json
    ├── eval_enzh.json
    ├── eval_enzh_wmt.json
    ├── merge_raw_encsdeisru.json
    └── merge_pol_encsdeisru.json

readme.md
```

`Annotation` 負責人工標註及標註分析；`TrainingData` 儲存 SFT 訓練資料與評估資料；`Evaluation` 則儲存評估 notebook 及 LLaMA／Qwen 的生成結果。

---

## `Annotation/`：人工標註與 translationese 分析

### `Annotation/translationese_error_span/`

這裡是 translationese error span 的人工標註資料。

語言方向包含：

- `deen`：德文到英文
- `enzh`：英文到中文

每個 `task0`、`task1` 等目錄內，包含來自標註工具匯出的 JSON 檔案。這些資料不是單純的模型輸出，而是包含：

- 原文
- 翻譯
- 標註 span 的起點與終點
- translationese error 類別

`ana_annos.ipynb` 會讀取這些標註，並計算：

- `style_err_ratio`
- `avg_style_err_ratio`
- 不同模型類別的 translationese error 比例

Notebook 中使用的錯誤類型包括：

```text
Unnatural Phrase Flow
Unnatural Sentence Flow
```

它也直接列出不同模型的結果，包括 ALMA 與 GPT-4 Turbo 變體。

相關檔案：

- [`ana_annos.ipynb`](https://github.com/tatasauces/LLM_translationese/blob/main/Annotation/translationese_error_span/ana_annos.ipynb)
- [`enzh.tsr.pdf`](https://github.com/tatasauces/LLM_translationese/blob/main/Annotation/translationese_error_span/enzh.tsr.pdf)

### `Annotation/training_instances_translationese_error_span/`

這個目錄將 translationese span 標註整理成可供後續 SFT 或 TSR 分析使用的資料。

- `deen/`：德文到英文的訓練實例
- `enzh/`：英文到中文的訓練實例
- `pre_avg_esr_sft.ipynb`：計算人工標註中的 style error ratio，並製作 SFT／TSR 分析資料
- `sft_tsr.pdf`：輸出的 TSR 圖表

`pre_avg_esr_sft.ipynb` 中可看到，它會讀取標註 JSON，計算每筆資料的：

```text
style_err
style_err_ratio
avg_esr
```

因此這個目錄的功能偏向「從人工標註建立訓練或分析用實例」。

### `Annotation/translationese_vote/`

這裡是 translationese 判斷或模型輸出比較的人工投票資料。

主要檔案：

- `deen_vote.xlsx`：德文到英文的人工投票
- `enzh_vote.xlsx`：英文到中文的人工投票
- `vote_keys.json`
- `vote_keys_deen.json`：投票資料的對應 key
- `ana_vote_enzh.ipynb`：分析投票結果

---

## `TrainingData/`：訓練與評估資料

### `TrainingData/filter/`

這裡是依據 perplexity 條件篩選後的 raw 訓練資料：

```text
merge_raw.ppl_0.1.json
merge_raw.ppl_0.2.json
merge_raw.ppl_0.4.json
merge_raw.ppl_0.8.json
```

檔名中的 `ppl_0.1`、`ppl_0.2` 等看起來代表不同的 PPL filtering ratio 或 threshold 設定。

### `TrainingData/merge_sft_endezh/`

這是英文、德文、中文相關的 SFT 資料及評估資料。

關鍵檔案：

```text
merge_raw.json
merge_trans.json
merge_pol.json
```

可以把三者理解為三種訓練資料設定：

| 檔案 | 用途 |
|---|---|
| `merge_raw.json` | raw／未經特殊處理的資料 |
| `merge_trans.json` | 翻譯結果資料 |
| `merge_pol.json` | polished／潤飾後資料 |

另外：

- `eval_deen.json`：德文到英文評估資料
- `eval_enzh.json`：英文到中文評估資料
- `*_wmt.json`：WMT 評估資料

`merge_pol.json` 是目前 repo 中最直接對應「改寫後／潤飾後資料集」的檔案。

### `TrainingData/merge_sft_encsdeisruzh/`

這是更大範圍的多語言 SFT 資料，涵蓋：

- English–Chinese
- English–German
- English–Czech
- English–Icelandic
- English–Russian

關鍵檔案：

```text
merge_raw_encsdeisru.json
merge_pol_encsdeisru.json
```

其中：

- `merge_raw_encsdeisru.json`：raw 版本
- `merge_pol_encsdeisru.json`：polished 版本

這個目錄較可能是論文中多語言 SFT 實驗的主要訓練資料來源。

---

## `Evaluation/`：模型輸出與評估

### `Evaluation/eval_heuristics.ipynb`

這個 notebook 使用：

- NLTK
- Stanza
- 中文字元級 tokenization
- 詞性標註

計算：

- lexical density
- length variety

它會讀取 `llama_results` 下的模型輸出，從 prompt 中抽取英文或中文文本，再與模型預測結果比較。

### `Evaluation/eval_ppl.ipynb`

這個 notebook 使用 Hugging Face Transformers 載入：

```text
meta-llama/Llama-3.1-8B
```

並對模型生成的翻譯結果計算 perplexity。

主要流程是：

1. 載入模型與 tokenizer
2. 讀取 JSON 評估資料
3. 取得每筆資料的 `predict`
4. 計算單筆 PPL
5. 計算整體平均 PPL

### `Evaluation/llama_results/`

儲存 LLaMA 模型的生成結果，檔名可辨識不同資料設定，例如：

```text
merge_raw_*
merge_pol_*
merge_trans_*
```

以及不同 checkpoint、語言方向、WMT 評估集等。

### `Evaluation/qwen_results/`

儲存 Qwen 模型的生成結果，檔名包含：

```text
qwen_merge_raw_*
qwen_merge_pol_*
qwen_merge_trans_*
```

因此可用來比較不同訓練資料類型對 Qwen 翻譯輸出的影響。

---

## GPT-4 改寫資料的判讀

目前 repo 中可以確認三層資訊：

### 1. 有 GPT-4 相關人工分析資料

`ana_annos.ipynb` 明確出現以下 model type：

```text
gpt-4-turbo_trans
gpt-4-turbo_end2end
gpt-4-turbo_ctx_pivot
```

也有 ALMA：

```text
alma_7B_trans
alma_13B_trans
```

### 2. 有 GPT-4 Polish 的分析標籤

Notebook 的圖表將其中一類命名為：

```text
GPT-4-Polish
```

這表示研究流程中確實存在 GPT-4 潤飾／改寫版本，並且該版本被納入 translationese error analysis。

### 3. 有 polished 訓練資料檔案

`TrainingData` 裡的：

```text
merge_sft_endezh/merge_pol.json
merge_sft_encsdeisruzh/merge_pol_encsdeisru.json
```

應是供 SFT 實驗使用的 polished 資料集，並且 `Evaluation` 目錄存在對應的 `merge_pol` 模型預測結果。

但需要注意：

- `merge_pol.json` 內沒有在檔名層級直接寫出 `GPT-4`
- README 沒有說明 `pol` 的具體生成模型
- repo 沒有提供 GPT-4 API prompt、批次改寫腳本或完整 provenance metadata

因此不能把它嚴格描述成「已明確證明的 GPT-4-only dataset」。最保守且符合 repo 證據的說法是：

> `merge_pol` 是研究中 polished／潤飾後的 SFT 資料；annotation notebook 顯示 GPT-4-Polish 是其中被研究與標註的模型條件，但 repo 沒有完整記錄每筆 `merge_pol` 資料的生成模型。

---

## 目前 repo 的文件狀態

這個 repo 幾乎沒有傳統文件結構：

- 沒有 `docs/`
- 沒有 `ARCHITECTURE.md`
- 沒有 `CONTRIBUTING.md`
- 沒有 `requirements.txt`
- 沒有 `pyproject.toml`
- 沒有 `environment.yml`
- 沒有 Makefile 或執行腳本

唯一的說明文件是 [`readme.md`](https://github.com/tatasauces/LLM_translationese/blob/main/readme.md)，內容只有簡短說明：

- `Annotation`：人工標註、解析與視覺化
- `Evaluation`：不同模型的翻譯結果與評估腳本
- `TrainingData`：論文實驗所使用的訓練資料

所以目前 repo 比較像是：

> 研究資料快照與 notebook／輸出歸檔，而不是一個可以直接 clone 後一鍵重現全部實驗的完整軟體專案。

另外，repo 中有多個 `.DS_Store` 檔案，這些是 macOS Finder metadata，與研究流程無關，可以忽略。
