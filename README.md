# 鹽行月租套房 — RWD + PWA 網站

本專案在原本的 `demo.html` 基礎上，加入了響應式網頁設計 (RWD) 微調與漸進式網路應用程式 (PWA) 功能。

## 檔案結構

```
.
├── index.html                  # 主頁面（原 demo.html，已加入 PWA 標籤與 RWD 微調）
├── manifest.json                # PWA App Manifest
├── sw.js                        # Service Worker（離線快取）
├── offline.html                 # 離線時的後備頁面
├── icons/
│   ├── icon-192.png
│   ├── icon-512.png
│   ├── icon-maskable-192.png
│   └── icon-maskable-512.png
└── README.md
```

## 這次做了什麼

### RWD（響應式設計）
- Hero 區塊、標題字級、間距針對手機/平板/桌機分級調整（`text-3xl sm:text-5xl lg:text-6xl` 等）。
- 商品卡片圖片改用 `loading="lazy"` 延遲載入，並依螢幕縮小圖片高度，加快行動裝置載入速度。
- `viewport` meta 加上 `viewport-fit=cover`，讓內容在有瀏海/圓角螢幕的裝置上正確顯示安全區域。
- 原本的 grid 版面（1 欄 / 2 欄 / 3 欄）與導覽列已具備良好響應式行為，保留並微調間距。

### PWA（漸進式網路應用程式）
- 新增 `manifest.json`：定義 App 名稱、圖示、主題色、`display: standalone`，可讓使用者「加入主畫面」，以類似原生 App 的體驗開啟。
- 新增 `sw.js` Service Worker：
  - 安裝時預先快取 App Shell（HTML、manifest、icons）。
  - 頁面導覽採 Network First，離線時自動顯示 `offline.html`。
  - 站內靜態資源採 Cache First。
  - 外部 CDN 資源（Tailwind、字型等）採 Stale-While-Revalidate，加速重複造訪並支援離線瀏覽已快取內容。
- 導覽列新增「安裝應用程式」按鈕，監聽 `beforeinstallprompt` 事件，讓使用者可在支援的瀏覽器（如 Chrome、Edge、Android）一鍵安裝。
- 新增多尺寸圖示（192px / 512px，含 maskable 版本），符合 Android/iOS 安裝規範。

## 本機測試方式

PWA 的 Service Worker 需要在 **HTTPS** 或 **localhost** 環境下才能運作，直接用瀏覽器開啟 `file://` 無法註冊成功。建議用以下任一方式在本機啟動伺服器測試：

```bash
# 方法一：使用 Python 內建伺服器
cd pwa-site
python3 -m http.server 8000
# 瀏覽器開啟 http://localhost:8000

# 方法二：使用 Node.js 的 serve
npx serve .
```

開啟後可用 Chrome DevTools 的 **Application** 分頁檢查 Manifest 與 Service Worker 是否註冊成功，或用 **Lighthouse** 檢測 PWA 分數。

## 部署到正式環境

上線時請確保：
1. 網站以 **HTTPS** 提供服務（PWA 必要條件）。
2. `manifest.json`、`sw.js`、`icons/` 與 `index.html` 位於同一層目錄（或依實際路徑調整 `sw.js` 內 `APP_SHELL` 與 `manifest.json` 內 `icons` 路徑）。
3. 若日後修改網站內容，記得更新 `sw.js` 內的 `CACHE_VERSION`，才能讓使用者端快取更新。

## 保留原有功能

原本的購物車 / GA4 電子商務事件追蹤 (`view_item`、`add_to_cart`、`view_cart`、`remove_from_cart`、`begin_checkout`、`add_payment_info`、`purchase`)、Supabase 預留設定區塊皆完整保留，行為未變動。
