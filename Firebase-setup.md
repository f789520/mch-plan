# 線上共同編輯：Firebase 設定教學（約 5 分鐘）

工具用 Firebase（Google 雲端）做即時同步。資料存在你自己的 Google 專案裡，只有拿到「房間名稱」的人能編輯。免費額度對這種用途綽綽有餘。

## 一、建立 Firebase 專案

1. 到 https://console.firebase.google.com/ ，用你的 Google 帳號登入。
2. 點「建立專案」，名稱隨意（例如 `mch-plan`），下一步的 Google Analytics 可以關掉，建立。

## 二、開啟即時資料庫（Realtime Database）

1. 左側選單 → 「建構 Build」→「Realtime Database」。
2. 點「建立資料庫」，位置選 `Singapore` 或預設都可。
3. 安全規則選 **「以測試模式啟動」**（Start in test mode）→ 啟用。
   - 注意：測試模式預設 30 天後任何人都不能讀寫。長期用請見文末「安全規則」。

## 三、開啟匿名登入

1. 左側 →「建構」→「Authentication」→「開始使用」。
2. 「Sign-in method」分頁 → 點「匿名 Anonymous」→ 啟用 → 儲存。
   - 這讓每個開網頁的人不用註冊就能連線。

## 四、拿到設定 JSON

1. 左上齒輪 →「專案設定」→ 最下方「你的應用程式」→ 點 `</>`（網頁）圖示。
2. 取一個暱稱，註冊應用程式（不用勾 Hosting）。
3. 畫面會出現一段 `const firebaseConfig = { ... }`，把大括號 `{ ... }` 這段複製起來。它長這樣：

```json
{
  "apiKey": "AIza................",
  "authDomain": "mch-plan.firebaseapp.com",
  "databaseURL": "https://mch-plan-default-rtdb.asia-southeast1.firebasedatabase.app",
  "projectId": "mch-plan",
  "appId": "1:......:web:......"
}
```

> 重要：一定要有 `databaseURL` 這一行。如果複製出來沒有，回 Realtime Database 頁面把最上面那條網址補進去。
> JSON 的 key 要有雙引號；Firebase 給的原始碼是 JS 物件（key 沒引號），貼進工具前把每個 key 加上雙引號，或直接用本檔上面的範例格式改。

## 五、在工具裡連線

1. 打開空間規劃工具 → 右側「共用」分頁。
2. 「房間名稱」填一個只有你們知道的字（例如 `mch-2f-a7k3`），當密碼用。
3. 「Firebase 設定 JSON」貼上第四步的 JSON。
4. 按「連線共用」，狀態變綠色「已連線」就成功。
5. 按「複製共用連結」，把連結傳給設計師/夥伴。他們打開後，**貼上同一份 Firebase JSON、用同一個房間名稱**，就能一起編輯，改動即時同步。

之後同一台電腦會記住設定，重開自動連線。

## 常見狀況

- **對方看不到我的圖**：確認三件事都一樣——網址、房間名稱、Firebase JSON（同一個專案）。
- **顯示連線失敗**：多半是 `databaseURL` 漏了，或 Authentication 的匿名登入沒開。
- **測試模式到期**：30 天後讀寫會被擋。到 Realtime Database →「規則」，改成下面這組（僅登入者可讀寫，比公開安全）：

```json
{
  "rules": {
    "rooms": {
      "$room": {
        ".read": "auth != null",
        ".write": "auth != null"
      }
    }
  }
}
```

## 資料存在哪、誰看得到

- 資料存在你 Firebase 專案的 Realtime Database，屬於你的 Google 帳號。
- 拿到「網址＋房間名稱＋Firebase JSON」三者的人才能編輯。房間名稱就是實質密碼，別公開貼。
- 工具本身若放上 GitHub Pages，是公開網頁（任何人拿到網址都能打開空白工具），但**看不到你的資料**，除非他也有你的房間名稱和 Firebase 設定。
