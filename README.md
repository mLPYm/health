# 慢慢生活 — 第一階段 PWA

這是可以部署的純靜態網站（HTML / CSS / JavaScript），不是設計稿。資料以瀏覽器 IndexedDB 保存，不使用後端，不依賴 CDN，也沒有追蹤器。

## 本機啟動

在 `life-pwa/` 資料夾開啟終端機：

```bash
python3 -m http.server 8080
```

以 `http://localhost:8080` 開啟。**不要直接用 file:// 雙擊 HTML 作為正式使用方式。**

## 安裝到 iPhone 主畫面

將整個 `life-pwa` 資料夾原樣部署到支援 HTTPS 的靜態網站託管（例如 GitHub Pages / Cloudflare Pages / Netlify）。使用 iPhone Safari 前往 HTTPS 網址 → 分享 → 加入主畫面。首次連線載入後，Service Worker 會快取 App 基本檔案供離線啟動。

**提醒：** iOS 不保證本機儲存永久不被清除。清除 Safari 網站資料、刪除網頁 App、低儲存空間的系統處理等都可能造成損失，請定期 ZIP 備份。部署到不同來源網址不會自動搬移原 IndexedDB 資料。主畫面 Web App 也可能使用獨立的網站儲存容器，請在實際安裝後確認資料並保留備份。

## 已完成

- 三分頁（今天 / 總結 / 設定）、深淺色自動切換、響應式 iPhone 直向版面
- IndexedDB 持久保存四類資料：records、habits、prefs、photos；編輯、刪除與重載持久化
- 睡眠起迄、跨日、補登、分段與主要睡眠標記，睡眠重疊檢查
- 主睡眠醒來後建立 18 小時三階段戒糖週期：如果再度入睡，以實際入睡提前截斷；可在階段內記錄含糖飲料，階段結束才確認無糖
- 飲水快捷鍵、累積水量、單筆編輯、刪除與當次誤觸復原
- 飲食文字、選填餐別、飽足程度、份量與本機壓縮圖片
- 體重記錄、簡易趨勢圖與近 7 日按每天首筆代表值計算的平均
- 自訂習慣（次數／時間／數值／單次／手動避免型）、停用、永久刪除
- 月曆方格牆，分類切換，空白不當失敗，點日期看紀錄
- JSON 純文字備份、未壓縮標準 ZIP（JSON+照片）備份；ZIP/JSON 合併匯入
- manifest、192/512 圖示、離線靜態檔快取

## 第一階段已知限制 / 下一階段

- 尚未實作瀏覽器通知或 iOS Web Push（不能用背景定時器當可靠提醒）
- 睡眠趨勢尚未有完整的日期／規律性圖表
- 飲食照片已保存，但尚未有照片瀏覽入口（可從 ZIP 取出）
- 體重跨類型分析、每週目標排程的顯示規則、全年總覽、資料匯入衝突預覽等留待下一階段
- `avoid` 自訂模式目前用一般習慣事件記錄，不具有戒糖專用三階段介面
- ZIP 為未壓縮格式，較大但相容性高；匯入只支援本 App 匯出的 ZIP（無壓縮、UTF-8）
- 系統內建習慣不能永久刪除，以防破壞內建紀錄類型；可停用。自訂習慣可二次確認後永久刪除歷史。
- 同一曆日多次主要睡眠可以建立多個週期；主頁顯示最近一個。完整的歷史週期選擇頁面留待下一階段。

## 主要資料結構

`records`: `{id,type,at,localDate,createdAt,updatedAt,offsetMinutes,...}`；睡眠額外 `{start,end,primary}`；戒糖 `{cycleId,stage,status,key}`；照片以 `photoId` 連結。

`habits`: `{id,name,icon,color,category,mode,unit,target,schedule,scheduleValue,active,systemType?}`。

`prefs`: `{id,weightStart,weightGoal,waterGoal,sleepTarget,wakeTarget}`。

`photos`: `{id,name,mime,blob,createdAt}`。

時區以建立紀錄當下的 UTC offset（分鐘）與 ISO UTC 時間保留；`localDate` 保留填寫當下的當地日期。**未來加入跨時區更動、雲端同步或多裝置衝突處理時，建議擴展 IANA 時區名稱與伺服器版本欄位。**
