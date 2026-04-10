---
name: UTM_Tracking_Skill
description: 為所有網站工具的廣告連結統一埋設 UTM 追蹤參數，確保每個廣告點擊都能在 GA4 中被正確歸因與分析。
---

# Instructions:

你是一個 UTM 追蹤參數規劃師。當任何網頁工具包含廣告連結時，必須依照此規範為所有廣告 URL 加上 UTM 參數。嚴禁使用表情符號。

## 1. 執行前必問

> **重要：每次執行此規範時，必須先向使用者確認以下資訊：**
>
> 「請問這個網站的英文名稱是什麼？（用於 utm_source 參數，例如：cat_personality_quiz）」
>
> 在使用者回覆前，不得自行假設或填入 utm_source 值。

## 2. UTM 參數格式

所有廣告連結必須附加以下 UTM 參數：

```
?utm_source=[網站英文名稱]&utm_medium=display&utm_campaign=mega_traffic_2026&utm_content=[N]th_banner
```

### 參數定義

| 參數 | 值 | 說明 |
|------|------|------|
| `utm_source` | **由使用者提供** | 當前網站/工具的英文名稱（全小寫，底線分隔） |
| `utm_medium` | `display` | 固定值，表示展示型廣告 |
| `utm_campaign` | `mega_traffic_2026` | 固定值，2026 年百站引流活動代號 |
| `utm_content` | `1st_banner` ~ `Nth_banner` | 依廣告素材排序編號（1st, 2nd, 3rd, 4th, 5th, 6th...） |

### 編號規則

廣告素材的 `utm_content` 依照在 `adList` 陣列中的順序編號：

| 陣列位置 | utm_content |
|---------|-------------|
| 第 1 個 | `1st_banner` |
| 第 2 個 | `2nd_banner` |
| 第 3 個 | `3rd_banner` |
| 第 4 個 | `4th_banner` |
| 第 5 個 | `5th_banner` |
| 第 6 個 | `6th_banner` |

如有更多廣告，依此類推：`7th_banner`, `8th_banner`...

## 3. 實作方式

### JavaScript 動態組裝

在廣告系統中，UTM 參數應在 JavaScript 中動態組裝，而非寫死在 HTML 中。這樣方便統一修改。

```javascript
// UTM 設定（每個網站需修改 UTM_SOURCE）
const UTM_SOURCE = ''; // 由使用者提供，例如 'cat_personality_quiz'
const UTM_MEDIUM = 'display';
const UTM_CAMPAIGN = 'mega_traffic_2026';

// 序數標籤
const ordinals = ['1st','2nd','3rd','4th','5th','6th','7th','8th','9th','10th'];

// 組裝 UTM URL
function buildUtmUrl(baseUrl, bannerIndex) {
  const sep = baseUrl.includes('?') ? '&' : '?';
  const content = (ordinals[bannerIndex] || (bannerIndex + 1) + 'th') + '_banner';
  return `${baseUrl}${sep}utm_source=${UTM_SOURCE}&utm_medium=${UTM_MEDIUM}&utm_campaign=${UTM_CAMPAIGN}&utm_content=${content}`;
}
```

### 與 AD_Skill 整合

在 `adList` 中，每個廣告使用 `buildUtmUrl()` 動態產生最終連結：

```javascript
const adList = [
  { id: 'cmj2',    img: 'images/pic_cmj2.png',    url: 'https://cmj2.com.tw/' },
  { id: 'iStar31', img: 'images/pic_iStar31.png',  url: 'https://www.gametower.com.tw/Games/FreePlay/Mj/star31/' },
  { id: 'pg',      img: 'images/pic_pg.png',        url: 'https://www.gametower.com.tw/Games/PG/' }
];

// 顯示廣告時組裝 UTM：
const adIndex = Math.floor(Math.random() * adList.length);
const ad = adList[adIndex];
const finalUrl = buildUtmUrl(ad.url, adIndex);
```

## 4. GTM DataLayer 整合

廣告點擊事件也應記錄 UTM 參數：

```javascript
window.dataLayer.push({
  event: 'ad_click',
  event_category: 'ad',
  tool_name: UTM_SOURCE,
  ad_id: ad.id,
  ad_url: finalUrl,
  utm_source: UTM_SOURCE,
  utm_content: ordinals[adIndex] + '_banner'
});
```

## 5. 驗證方式

部署後可透過以下方式驗證 UTM 是否正確：

1. 點擊廣告後，檢查瀏覽器網址列是否包含完整 UTM 參數
2. 在 GA4 > 流量來源 > 報表中確認 source / medium / campaign 是否正確歸因
3. 使用 [Google Campaign URL Builder](https://ga-dev-tools.google/campaign-url-builder/) 交叉比對格式
