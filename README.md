# JLPT(單字)

N5～N4 日文單字與漢字的每日學習 App。純前端的 PWA，不需要後端，可以安裝到手機主畫面，離線也能使用。
link : https://chekaikaikai.github.io/JLPT_V2/

## 功能

- **每日進度**：每天自動挑選新單字、新漢字，先「學習」再「測驗」。
- **間隔複習**：答對後依 1、3、7、14、30 天的間隔安排複習，答錯會回到學習中並再考一次。
- **多種測驗題型**：日文選中文、中文選日文、假名輸入漢字、漢字輸入假名。
- **錯題本**：答錯自動加入，之後答對就移出。
- **單字與漢字列表**：可搜尋、依等級與學習狀態篩選，點開可看詳細內容與例句。
- **假名標註（furigana）**：可在單字列表切換顯示或隱藏。
- **🔊 發音**：按下喇叭按鈕，朗讀單字的假名讀音或整句例句（見下方說明）。
- **學習統計**：連續學習天數、本週學習量、長期記憶率。
- **備份**：可匯出與匯入進度（JSON）。

## 檔案結構

```
.
├── index.html               # 整個 App（程式、樣式、單字資料都在這一個檔案）
├── manifest.webmanifest     # PWA 設定（名稱、icon、顏色）
├── sw.js                    # Service Worker（離線快取）
├── icons/
│   ├── icon-32.png          # 瀏覽器分頁 favicon
│   ├── icon-192.png         # 一般 icon
│   ├── icon-512.png         # 一般 icon
│   ├── icon-maskable-512.png# Android 自適應 icon（內容放在安全範圍內）
│   └── apple-touch-icon.png # iOS 主畫面 icon
└── README.md
```

## 部署

這是純靜態網站，把整個資料夾放到任何靜態主機即可，例如 GitHub Pages、Netlify、Cloudflare Pages。

注意：Service Worker 與「加入主畫面」都需要 **HTTPS**（`localhost` 除外）。

本機預覽：

```bash
python3 -m http.server 8000
# 瀏覽器開啟 http://localhost:8000
```

## 安裝到手機

- **iPhone（Safari）**：分享按鈕 → 加入主畫面。
- **Android（Chrome）**：選單 → 安裝應用程式 / 加到主畫面。

主畫面 icon 下方顯示的名稱來自 `manifest.webmanifest` 的 `short_name`（目前是「JLPT(單字)」），iOS 則取自 `index.html` 的 `apple-mobile-web-app-title`。

## 🔊 發音功能

使用瀏覽器內建的 Web Speech API（`speechSynthesis`），語言設為 `ja-JP`，不需要額外音檔，也不需要網路 API。

喇叭按鈕出現的位置：

| 位置 | 朗讀內容 |
| --- | --- |
| 單字詳細頁、學習卡片 | 該單字的假名讀音 |
| 例句 | 整句例句，漢字會改用假名朗讀，避免讀錯 |
| 漢字詳細頁的「常見單字」 | 該單字的假名讀音 |
| 測驗「日文選中文」題 | 題目單字的讀音 |
| 測驗作答後 | 該單字的讀音，方便複誦 |

測驗中的「漢字輸入假名」題不提供喇叭，因為聽到讀音就等於看到答案。

### 使用限制

- 必須由使用者**點擊按鈕**才會發聲，不能自動播放（iOS 的限制）。
- 聲音品質取決於裝置內建的日文語音。iPhone 通常有 Kyoko；Android 需要有 Google 日文語音。
- 如果裝置沒有日文語音，App 會提示「找不到日文語音」，請到系統設定安裝日文語音。
- 離線時要裝置已安裝語音才能使用。
- 重音（pitch accent）可能不夠自然，語音僅作為輔助。

## 修改與更新

### 更換 icon

替換 `icons/` 內的圖檔後，請同時：

1. 保持檔名與尺寸不變，或同步修改 `manifest.webmanifest`、`index.html` 與 `sw.js` 裡的路徑。
2. 把 `sw.js` 第一行的 `CACHE` 版本號加一（例如 `jlpt-v6` → `jlpt-v7`），讓使用者端重新下載。
3. 已經安裝的使用者通常要**刪掉再重新加入主畫面**，系統才會換成新 icon。

maskable icon 的重要內容要放在中央約 80% 的範圍內，否則被圓形或其他形狀裁切時會被切掉。

### 更新程式

`sw.js` 採用「網路優先」策略：有網路時一律載入最新版，離線時才用快取。更新 `index.html` 後重新部署即可；若有新增檔案，記得加進 `sw.js` 的 `FILES` 清單，並調高 `CACHE` 版本號。

## 資料與隱私

- 學習進度只存在這個瀏覽器的 `localStorage`（key：`nihongo-daily-v2`），**不會上傳到任何伺服器**。
- 換手機或清除瀏覽資料之前，請先到「設定」→「備份進度」匯出，之後再匯入。

## 資料來源與授權

單字與漢字資料來自 OpenJLPT（CC BY-SA 4.0），內容包含 JMdict、KANJIDIC2、Jonathan Waller 的 JLPT 清單與 Tatoeba 例句。

- JLPT 官方沒有公布正式字表，等級為社群整理的近似值。
- 繁體中文翻譯為草稿，尚待校對。
- 因為資料採用 CC BY-SA 4.0，散布或修改時請保留出處，並以相同授權分享衍生資料。
