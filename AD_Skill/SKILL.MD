---
name: AD_Skill
description: 測驗完成後隨機彈出廣告的通用規範，管理廣告素材、投放邏輯與 GTM 追蹤事件。
---

# Instructions:

你是一個廣告投放規劃師。當任何心理測驗或互動工具完成時，必須依照此規範隨機彈出一則廣告。嚴禁使用表情符號。

## 1. 廣告素材清單

所有廣告圖片存放於 `AD/` 目錄下。每則廣告包含圖片路徑與目標連結。

| 素材 ID | 圖片檔案 | 目標連結 |
|---------|---------|---------|
| cmj2 | `../AD/pic_cmj2.png` | https://cmj2.com.tw/ |
| iStar31 | `../AD/pic_iStar31.png` | https://www.gametower.com.tw/Games/FreePlay/Mj/star31/ |
| pg | `../AD/pic_pg.png` | https://www.gametower.com.tw/Games/PG/ |

> **新增廣告**: 將圖片加入 `AD/` 目錄，並在此表格新增一筆即可。工具端的 JavaScript 會自動從清單中隨機選取。

## 2. 投放邏輯

### 觸發時機
- 測驗結果頁載入後，延遲 **1.5 秒** 彈出廣告 Modal。
- 使用者關閉後不再重複彈出（同一次測驗週期內）。
- 使用者點擊「重新測驗」後重置狀態，下次完成可再次彈出。

### 隨機選取
- 從廣告清單中以 `Math.random()` 均勻隨機選取一則。
- 每次彈出僅顯示一則廣告。

### Modal 行為
- 使用全螢幕半透明遮罩 (`rgba(0,0,0,0.7)`)。
- 廣告卡片置中顯示，帶淡入動畫。
- 右上角顯示關閉按鈕 (X)。
- 點擊廣告圖片：以 `_blank` 新分頁開啟目標連結。
- 點擊關閉按鈕或遮罩區域：關閉 Modal。
- 支援 `Escape` 鍵關閉。

## 3. UI 設計規範

### Modal 樣式
```css
/* 遮罩 */
.ad-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.7);
  backdrop-filter: blur(8px);
  z-index: 1000;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* 卡片 */
.ad-card {
  position: relative;
  max-width: 480px;
  width: 90%;
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);
  animation: adPopIn 0.4s ease-out;
}

/* 關閉按鈕 */
.ad-close {
  position: absolute;
  top: 8px;
  right: 8px;
  width: 32px;
  height: 32px;
  background: rgba(0, 0, 0, 0.6);
  border: none;
  border-radius: 50%;
  color: white;
  font-size: 18px;
  cursor: pointer;
}
```

### 廣告圖片
- 使用 `width: 100%`，保持原始比例。
- 加上 `cursor: pointer`。
- Hover 時輕微放大 (`transform: scale(1.02)`)。

## 4. GTM 追蹤事件

| 事件名稱 | 觸發時機 | 參數 |
|----------|---------|------|
| `ad_impression` | 廣告 Modal 顯示 | `ad_id`, `tool_name` |
| `ad_click` | 點擊廣告圖片 | `ad_id`, `ad_url`, `tool_name` |
| `ad_close` | 關閉廣告 Modal | `ad_id`, `close_method` (button / overlay / escape), `tool_name` |

```javascript
// 範例
window.dataLayer.push({
  event: 'ad_impression',
  event_category: 'ad',
  tool_name: 'cat_personality_quiz',
  ad_id: 'cmj2'
});
```

## 5. 無障礙設計

- Modal 開啟時設定 `aria-modal="true"` 與 `role="dialog"`。
- 關閉按鈕加上 `aria-label="關閉廣告"`。
- 開啟 Modal 時 focus 到關閉按鈕。
- 關閉 Modal 後 focus 回到觸發元素。
