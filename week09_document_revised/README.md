# 第9週文件處理｜修訂版App

本App的流程：**讀取 → 清理 → 切塊 → 檢查 → 匯出**，為第10週語意搜尋與第11週RAG準備資料。
搭配上層檔名含「課堂簡化版_修訂版」的學生／教師Notebook。原課堂簡化版（`week09_document_simple/`）與原範例資料夾`sample_tata/`保留未改。

## 學生從這裡開始

1. 在Notebook看完短示範，完成引導練習A（清理）並通過自我檢查；活動B用已提供的切塊函式比較兩組參數，填寫觀察表。
2. 只把完成的`clean_text`貼到`student/app.py`的同名函式（保留`import re`）。讀取、切塊與畫面都已提供。
3. 啟動App，資料來源選「內建範例」：先跑`sample.txt`，再換`sample.csv`、`sample.pdf`、`sample.docx`。A未完成時會顯示提示，不會有假的成功結果。
4. 固定同一份文件與chunk_size，只改overlap；下載兩份JSON讀回，挑一個問題找出支持答案的片段與原文，核對日期或數字。
5. 作業沿用同一個App換一份文件；並下載「清理後文字」保留給第10週使用。完整規格與評分在Notebook第7節。

教師使用`teacher/app.py`；請勿把教師程式或教師Notebook放進學生發放資料。

## 範例檔（Notebook與App共用）

四個範例都在[sample_data](sample_data/README.md)：`sample.txt`、`sample.csv`、`sample.pdf`、`sample.docx`，皆為虛構活動資料。

- **App：**資料來源選「內建範例」，從下拉選單選檔即可，不必上傳。
- **Notebook：**先把這四個檔案複製到Notebook所在的資料夾（`week09/`）；Colab直接上傳。範例程式只寫檔名。
- Notebook第6節與App選`sample.txt`、size=120、overlap=20時，結果應該相同。

## Windows／Python 3.14安裝與啟動

在課程Repo根目錄先進入本資料夾：

```powershell
cd week09/week09_document_revised
py -3.14 -m venv .venv
.\.venv\Scripts\Activate.ps1
python --version
python -m pip install -r requirements.txt
python -m pip check
python -m streamlit run student/app.py
```

- 已有`.venv`時先確認版本；不要覆蓋原環境，可改建`.venv-py314`。VS Code請選取該環境的`Scripts/python.exe`，安裝與啟動要用同一個環境。
- 若PowerShell阻擋啟用指令，可直接使用`.\.venv\Scripts\python.exe -m pip ...`與`.\.venv\Scripts\python.exe -m streamlit run student/app.py`。
- 教師版：`python -m streamlit run teacher/app.py`。也可以從Repo根目錄執行`python -m streamlit run week09/week09_document_revised/student/app.py`；範例檔依程式位置讀取，不受目前目錄影響。
- Colab只執行Notebook短例子，不開Streamlit。部署是選做，共通操作見[第7週起Git指南](../../week07/生成式AI應用開發_第07週_Git實作教材_學期共用Repo版.md)。

## 檔案與責任

| 檔案 | 內容 |
|---|---|
| `student/app.py` | 學生A清理骨架；完整切塊、讀取與畫面 |
| `teacher/app.py` | 相同流程的完整參考答案 |
| `sample_data/` | 與Notebook相同的四個範例檔 |
| `requirements.txt` | App與PDF／Word讀取套件 |
| `requirements-api.txt` | 只有Notebook選做AI摘要需要 |
| `.gitignore` | 排除環境、快取與真實金鑰設定 |

## 操作規格

App沒有API功能，全程0次請求，不需要`.env`或Secrets。資料來源、編碼與參數都在表單裡，按「處理文件」才讀取；改了值但沒按，畫面仍標示為上次成功結果。

- 新提交先清掉舊結果，全部通過才保存。按清除、A未完成、空白檔、解碼錯誤、檔案損壞或參數不合理，都不會留下舊的下載。
- 預覽片段或下載只使用已保存的資料，不重新讀檔。

| 限制 | 處理方式 |
|---|---|
| 檔案1MB、PDF50頁、CSV200列 | 超過就拒絕，不會偷偷截斷 |
| 清理後1～20000字元 | 超過或沒有內容就拒絕 |
| TXT／MD／CSV編碼 | 上傳檔案預設UTF-8-SIG，可選CP950；不自動猜測、不忽略錯誤。內建範例固定UTF-8 |
| CSV | 逗號分隔，保留欄名與每一列；空欄名、重複欄名或欄數不合會被拒絕 |
| PDF | 讀文字層；全部沒有文字時提示可能需要OCR，部分無文字頁保留頁碼與提示 |
| Word | 依本文順序讀段落與表格；不保證圖片、巢狀表格、頁首頁尾完整 |
| 片段位置 | `text == cleaned_text[start:end]`；end不包含，位置不是PDF座標 |

以上是課堂小文件的範圍，不是公開服務的完整防護。讀取成功後，仍要對照原檔核對日期、數字與順序。

## 兩種下載

| 下載 | 內容 | 用途 |
|---|---|---|
| `document.chunks.json` | filename、encoding、character_count、chunk_size、overlap、raw_text、cleaned_text、chunks（每個含chunk_id、start、end、text） | 本週作業：比較兩組參數、讀回驗證start:end |
| `cleaned_text.txt` | 清理後文字（段落以空白行分隔） | 第10週：貼進語意搜尋App的文字框，以空白行分段 |

第10週App不會匯入JSON；請一併保留原文件與清理後文字。CSV清理後每列之間沒有空白行，若想讓每一列各成一段，貼入前在列與列之間加空白行。

## 練習、作業與繳交

- 核心150分鐘，另留休息10分鐘與排障20分鐘。A是清理實作，B是參數比較與來源判讀；App只需放入A。
- 回家作業45～60分鐘：交同一個App、完成A與B紀錄的學生Notebook、原文件、兩份JSON、清理後文字與README；不另做第二個App或手寫切塊。
- README至少寫：用途與來源、啟動方式與實際版本、兩組參數與邊界實例、一個問題及兩組支持答案的chunk_id與原文、一個錯誤或邊界與處理、核對的日期或數字、限制與未測項目。
- 推送到整學期自己的Repo；老師從既有表單登記的Repo查看，不另交每週資料夾連結。

## 選做摘要

Notebook第9節自帶短文字，預設`RUN_PAID_API=False`（0次）；基本作業不需要開啟。

```powershell
python -m pip install -r requirements-api.txt
```

金鑰只放在環境變數或自己的`.env`；Colab使用Secrets並授權Notebook。不要上傳真實金鑰、`.env`或`secrets.toml`。
每次有效執行最多1次請求、關閉自動重試、最多6000字元、不截斷。`store=False`不代表資料留在本機；模型可用性與品質需課前用教學帳號確認。

## 第10、11週銜接

- 第10週：把確認過的清理後文字貼進語意搜尋App（以空白行分段，最多12段）。
- 第11週：自行建置RAG或OpenAI File Search擇一。File Search由平台處理支援文件的切塊、向量化與索引；需要上傳文件，有格式與費用條件，不保證CSV可直接上傳。
- 兩條路線都要求核對原文與來源；本週不建立任何平台資源。官方依據：[OpenAI Retrieval](https://developers.openai.com/api/docs/guides/retrieval)、[File Search](https://developers.openai.com/api/docs/guides/tools-file-search)。

## 教師重建與驗證

在課程Repo根目錄、啟用已安裝套件的環境後執行：

```powershell
python -X utf8 scripts/build_week09_revised.py
python -X utf8 scripts/verify_week09_revised.py
```

重建會產生兩份Notebook／Markdown、兩份App、requirements與`.gitignore`，並從`week09/sample_tata/`複製四個範例檔到`sample_data/`；本README、`sample_data/README.md`與教師導讀不會被覆蓋。
驗證結果與未測項目見[教師導讀](../第09週_課堂簡化版修訂版_教師導讀與修訂紀錄.md)；離線檢查與AppTest不等於真人瀏覽器、API、Colab、Cloud或試教通過。

---

## 我的作業紀錄（第 9 週）

**選擇與限制（3～5 句）：**我自己寫了一份虛構的「2027寒假實習說明會公告」當作業文件，延續期中專題的實習主題，內容有報名截止、活動日期、名額等可核對的日期與數字。為了讓切塊交界落在答案附近，固定 chunk_size=60，只比較 overlap=0 和 20。overlap=0 時報名截止時間被切成兩段，overlap=20 時完整落在同一段，所以我選 overlap=20，代價是片段數從 8 變 11、有重複文字。限制是文件只有 439 字、只測 TXT 上傳，而且這只是人工回查，還沒有向量搜尋。

### 檔案

| 檔案 | 內容 |
|---|---|
| `生成式AI應用開發_第09週_…_學生版.ipynb` | 從 Colab 下載的學生 Notebook：A 清理、B 觀察表、A 的「失敗→修正→重測」紀錄 |
| `student/app.py` | 只填入 A 的 `clean_text`，其餘是老師提供的程式 |
| `my_data/實習說明會公告_虛構.txt` | 作業用原文件（自己編寫的虛構資料） |
| `results/實習說明會_size60_overlap0.chunks.json`、`results/實習說明會_size60_overlap20.chunks.json` | 作業兩組參數的下載檔 |
| `results/實習說明會_cleaned_text.txt` | 清理後文字，留給第 10 週貼入語意搜尋 App |
| `results/sample_size120_overlap0.chunks.json`、`results/sample_size120_overlap20.chunks.json` | 課堂操作：sample.txt 兩組下載檔 |

### 7-2 實測紀錄表

| 項目 | 我的實測紀錄 |
|---|---|
| 用途／文件來源／授權 | 練習文件讀取、清理、切塊與來源回查。文件是自己編寫的虛構公告（學校、企業、分機都是虛構），沒有個資，可自由使用 |
| Python與套件實際版本、啟動命令 | Python 3.11.1、streamlit 1.63.0、pypdf 6.18.1、python-docx 1.2.0（課程指定的 3.14 本機尚未安裝）。在本資料夾執行 `python -m streamlit run student/app.py` |
| 原文日期／數字核對 | 原文「請於2026年12月2日中午12:00前」「2026年12月9日（星期三）13:30至16:30」「實習名額共45名」「座位限120人」「2027年1月18日至2月26日」，對照清理後文字全部一字不差。清理唯一的變化：標題與洽詢那行的全形空白「　」變成半形空白 |
| 同份文件：size、overlap、字元數、chunk數（兩組） | 實習說明會公告：size=60、overlap=0 → 439 字元、8 段；size=60、overlap=20 → 439 字元、11 段。（課堂 sample.txt：size=120，overlap=0 與 20 都是 121 字元、2 段） |
| 問題／兩組支持答案的chunk_id（start–end）與原文 | 見下表 |
| 邊界例子與選擇理由 | overlap=0 的 chunk 2／3 交界在第 180 字，剛好把「12月2日中｜午12:00」切開。選 overlap=20，因為完整截止時間會落在同一段 |
| 錯誤或邊界：輸入→預期→實際→修正→重測 | 見下方 |
| 清理後文字是否已保留給第10週 | 已保留：`results/實習說明會_cleaned_text.txt`（App 下載，段落以空白行分隔） |
| 尚存限制、已測／未測項目 | 已測：內建範例 sample.txt／csv／pdf／docx 都能處理；作業 TXT 上傳；兩組參數；overlap=size 錯誤；四份 JSON 讀回。未測：Python 3.14、CP950 編碼、掃描型 PDF、部署。API：0 次，未測 AI |

**問題：「說明會的報名截止時間是什麼時候？」**（App 上傳 `實習說明會公告_虛構.txt`，chunk_size=60）

| overlap | 片段數 | 支持答案的chunk_id（start–end） | 原文 | 觀察 |
|---|---|---|---|---|
| 0 | 8 | 2（120–180）＋3（180–240） | chunk 2 結尾「…報名方式：請於2026年12月2日中」；chunk 3 開頭「午12:00前，至職涯發展中心網站填寫報名表…」 | 截止時間被切成兩半，要兩段合起來才完整 |
| 20 | 11 | 4（160–220） | 「報名方式：請於2026年12月2日中午12:00前，至職涯發展中心網站填寫報名表。現場座位限120人…」 | 重疊讓完整截止時間落在同一段；chunk 3（120–180）結尾仍只有「12月2日中」 |

日期核對：原文寫「請於2026年12月2日中午12:00前」，兩組片段合起來都是這個日期與時間，清理與切塊沒有改到它。這是人工回查，不是搜尋準確率。

**錯誤或邊界：overlap = chunk_size**

1. 輸入：同一份文件，chunk_size=60、overlap=60，按「處理文件」。
2. 預期：拒絕處理，不留下上一筆（overlap=20）的結果與下載按鈕。
3. 實際：顯示「輸入或參數不合格：overlap須滿足0 ≤ overlap < chunk_size」，舊結果和兩個下載按鈕都消失。
4. 修正：把 overlap 改回 20。
5. 重測：處理成功，439 字元、11 段，下載按鈕重新出現。

### 課堂操作紀錄（內建範例，chunk_size=120、overlap=20）

| 範例 | 字元數 | 片段數 | 觀察 |
|---|---|---|---|
| sample.txt | 121 | 2 | 與 Notebook 第 6 節相同（121 字、2 段）；overlap=0 時是 0–120、120–121，overlap=20 時是 0–120、100–121 |
| sample.csv | 352 | 4 | 每列轉成「第n列：活動=…；日期=…」；引號內的「圖書館,討論室」完整保留；chunk 0 結尾把第 3 列切開 |
| sample.pdf | 747 | 8 | 有「[第1頁]」「[第2頁]」頁碼標記，日期 2026年11月11日、名額 32 人正確 |
| sample.docx | 493 | 5 | 「下表列出活動流程」段落 → 表格 → 「表格後的說明」順序正確 |

### 讀回驗證

用 Notebook 第 6-2 節同一段程式（檢查每個 `text == cleaned_text[start:end]`）讀回 `results/` 的四份 JSON，全部通過（片段數 2、2、8、11）。這一步在本機執行，沒有把檔案上傳到 Colab 再跑一次。

### AI 協助

使用 Claude（Claude Code）：編寫虛構作業文件；在本機啟動 App 並在瀏覽器實際操作（內建範例、上傳文件、兩組參數、錯誤邊界）；下載並讀回 JSON；整理本紀錄。A 的 `clean_text` 與 B 觀察表在 Colab Notebook 完成，包含一次「失敗→修正→重測」紀錄。

### 部署

本週部署是選做，未部署。
