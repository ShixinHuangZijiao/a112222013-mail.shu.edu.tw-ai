# 實習職缺整理小幫手（第 8 週期中個人小專題）

## 使用者與問題

**使用者：**正在找實習的大學生。

**問題：**實習職缺常貼在求職網站、粉專或群組，格式各不相同。有的寫得很完整，有的只有幾句「歡迎私訊」，連薪資、地點、時數都沒寫。學生要一則一則讀，很難快速看懂、比較，也容易忽略「原文根本沒寫」的重要資訊。

**這個 App 做的事：**貼上一則職缺原文，整理成 7 個固定欄位；原文沒寫的欄位明確標成「未提供」，並列出應徵前建議先詢問的事項。只依原文整理，不補猜行情或常識。

## 為什麼用 Pydantic

- 欄位、型別和用途說明寫在同一個 `ProjectResult` 類別裡，讀程式就能看到完整的資料契約。
- `responses.parse(text_format=ProjectResult)` 由 SDK 產生 strict JSON Schema，回傳後直接得到驗證過的物件，不用另外手寫 Schema 字典。
- `extra="forbid"` 與 `strict=True` 讓多餘欄位或型別錯誤直接被擋下，不會被默默轉型。

## 輸出欄位

| 欄位 | 型別 | 畫面標籤 | 用途 | 原文沒寫時 |
|---|---|---|---|---|
| `company_and_title` | str | 公司與職稱 | 一眼確認是哪個職缺 | 「未提供」 |
| `responsibilities` | list[str] | 工作內容 | 判斷工作是否符合興趣 | 空清單 |
| `requirements` | list[str] | 應徵條件 | 判斷自己是否符合資格 | 空清單 |
| `location_and_schedule` | str | 地點、時數與期間 | 判斷能否配合通勤與課表 | 「未提供」 |
| `salary` | str | 薪資／津貼 | 比較待遇，照原文數字與單位 | 「未提供」 |
| `how_to_apply` | str | 應徵方式與截止日 | 知道下一步怎麼投 | 「未提供」 |
| `missing_info` | list[str] | 原文沒寫、建議先問的事 | 應徵或面試前要問的問題 | 原文都寫了就是空清單 |

畫面上，值為「未提供」的欄位會用黃色警示框標出。

## 安裝與啟動（本機）

在本資料夾（`Midterm Mini-Project`）執行，使用 Python 3.14：

```powershell
py -3.14 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
Copy-Item .env.example .env
python -m streamlit run app.py
```

從 Repo 根目錄啟動則執行：`python -m streamlit run "Midterm Mini-Project/app.py"`。

## 設定方式

- **本機：**把 `.env.example` 另存成 `.env`，填入自己的 `OPENAI_API_KEY`。`.env` 已列在 `.gitignore`，不會提交。
- **Streamlit Cloud：**在 App 的 Settings → Secrets 貼上 `.streamlit/secrets.example.toml` 的格式並填入金鑰。
- 模型預設 `gpt-5.4-mini`，可用 `OPENAI_MODEL` 改。

## 操作範例

1. 打開 App，預設「啟用真實 API」是關閉的。
2. 貼上 `sample_data/正常職缺_虛構.txt` 的內容，按「整理職缺」：API 關閉時只檢查輸入，不產生結果，0 次請求。
3. 勾選「啟用真實 API」，再按一次「整理職缺」：送出 1 次請求，畫面依 7 個欄位顯示結果。
4. 換成 `sample_data/資訊不足職缺_虛構.txt`：預期薪資、地點、應徵方式等欄位顯示「未提供」，並列出建議詢問的事項。
5. 展開「本筆結果對應的職缺原文」，逐欄對照原文核對。

兩份範例都是自行編寫的虛構資料。

## 部署與雲端驗收

| 項目 | 內容 |
|---|---|
| App 網址 | 待部署後填寫 |
| Repo／分支／入口 | `ShixinHuangZijiao/a112222013-mail.shu.edu.tw-ai`／`main`／`Midterm Mini-Project/app.py` |
| Cloud Python 版本 | 待部署後填寫 |
| 雲端驗收日期與結果 | 待部署後填寫 |
| 老師觀看權限 | 待部署後確認 |

## 實測與未測

| 項目 | 方式 | 結果 |
|---|---|---|
| 空白、超過 6000 字 | 離線（Streamlit AppTest） | 顯示錯誤，未送出請求 ✅ |
| API 關閉時送出 | 離線 | 只顯示「尚未呼叫 API」，沒有假結果 ✅ |
| SDK 產生的 strict Schema | 離線 | 7 個欄位全部必填，禁止多餘欄位 ✅ |
| 成功結果顯示、「未提供」警示 | **模擬回應**（假的 client，非真實 API） | 7 個欄位依標籤顯示，未提供欄位出現警示 ✅ |
| 成功後再送出空白 | 模擬 | 舊結果被清掉，只顯示錯誤 ✅ |
| 模型拒答 | 模擬 | 顯示錯誤，不保存結果 ✅ |
| 缺金鑰 | 離線 | 在送出請求前停止 ✅ |
| 正常職缺（真實 API） | 未測 | 待設定金鑰後在雲端測試 |
| 資訊不足職缺（真實 API） | 未測 | 待設定金鑰後在雲端測試 |
| 真人瀏覽器、Cloud 部署 | 未測 | 待部署 |

離線與模擬測試只證明程式流程正確，**不代表模型實際輸出的品質**。測試環境是 Python 3.11＋Streamlit 1.63.0＋Pydantic 2.14.1，本機尚未安裝課程指定的 3.14。

## 成本與限制

- API 預設關閉；開啟後每次有效送出最多 1 次請求，`max_retries=0`。規劃測試共 2 次（正常＋資訊不足）。
- 原文限 6000 字元，超過就拒絕，不會偷偷截斷。字元數不等於 token 數。
- 格式通過不代表內容正確，仍要逐欄對照原文。
- 只處理一則文字職缺，不支援上傳檔案或一次比較多則。
- `store=False` 仍會把原文送到 OpenAI，不要貼上個資或機密。

## AI 協助

使用 Claude（Claude Code）：依我選定的題目，協助完成 A（任務規則）、B（`ProjectResult` 欄位）、C（標籤與結果區顯示）、兩份虛構測試資料、離線與模擬測試，以及本 README 和設計與驗收紀錄。真實 API 與雲端部署的測試要等我設定金鑰後親自執行並記錄。
