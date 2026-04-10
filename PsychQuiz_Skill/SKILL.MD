---
name: PsychQuiz_Skill
description: 心理測驗類網頁工具的通用建構規範，涵蓋測驗結構、計分邏輯、結果映射、GTM 追蹤事件、SEO 與雙語支援。
---

# Instructions:

你是一個心理測驗網頁工具建構專家。當需要產出心理測驗類的 HTML 網頁工具時，必須遵循以下規範。嚴禁在 UI 中使用表情符號。

## 1. 測驗結構標準

### 題目格式
- 每道題目必須為**情境式描述**，非直接詢問性格。
- 每題提供 **4 個選項**，每個選項對應不同人格類型的加分。
- 題目數量建議 **8-12 題**，確保使用者體驗流暢且結果準確。

### 計分邏輯
- 採用**多維度加權計分**：每個選項可同時為多個人格加分。
- 主要對應類型加 2 分，次要對應類型加 1 分。
- 最終結果取得分最高的類型；若平手則取第一個達到最高分的類型。

### 結果映射
- 每個結果類型必須包含：
  - `id`: 唯一識別碼
  - `name`: 結果名稱 (含翻譯)
  - `title`: 頁面標題 (含翻譯)
  - `description`: 200 字以內的性格描述 (含翻譯)
  - `traits`: 3-4 個性格關鍵字 (含翻譯)
  - `image`: 結果圖片路徑

## 2. UI/UX 規範

### 頁面流程
1. **Landing Page** — 測驗主題介紹 + 開始按鈕
2. **Question Page** — 逐題顯示 + 進度條 + 返回功能
3. **Result Page** — 結果圖片 + 性格分析 + 分享按鈕

### 互動設計
- 選擇選項後，自動延遲 400ms 滑入下一題。
- 進度條需即時更新，並附上「第 X / Y 題」文字。
- 返回上一題時保留已選答案。
- 結果頁使用漸入動畫 (fadeIn + translateY)。

### 響應式設計
- Mobile-first 設計策略。
- 最小支援寬度：320px。
- 卡片最大寬度：600px，水平置中。

## 3. 雙語支援 (i18n)

- 預設語言為**繁體中文 (zh-TW)**。
- 支援**英文 (en)** 切換。
- 使用 JavaScript 物件儲存翻譯字串。
- 語言切換按鈕置於頁面右上角。
- 切換語言時不重置測驗進度。

## 4. GTM 追蹤事件規範

依循 `GTM_Analytics_Skill` 規範，並額外定義以下事件：

| 事件名稱 | 觸發時機 | 必要參數 |
|----------|---------|---------|
| `quiz_start` | 點擊開始測驗 | `tool_name` |
| `quiz_answer` | 選擇答案 | `tool_name`, `question_number`, `selected_option` |
| `quiz_complete` | 顯示結果 | `tool_name`, `result_type` |
| `quiz_share` | 點擊分享 | `tool_name`, `share_platform`, `result_type` |
| `quiz_restart` | 重新測驗 | `tool_name`, `previous_result` |

```javascript
// 範例：推送測驗完成事件
window.dataLayer = window.dataLayer || [];
window.dataLayer.push({
  'event': 'quiz_complete',
  'event_category': 'quiz',
  'tool_name': 'cat_personality_quiz',
  'result_type': 'british_shorthair'
});
```

## 5. SEO 規範

### Meta Tags
- `<title>`: 含測驗主題關鍵字
- `<meta name="description">`: 描述測驗內容，含行動呼籲
- Open Graph tags: `og:title`, `og:description`, `og:image`, `og:type`

### 結構化資料 (JSON-LD)
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Quiz",
  "name": "測驗名稱",
  "description": "測驗描述",
  "educationalLevel": "beginner"
}
</script>
```

## 6. 無障礙設計 (Accessibility)

- 所有按鈕必須有 `aria-label`。
- 進度條使用 `role="progressbar"` + `aria-valuenow`。
- 圖片必須有描述性 `alt` 文字。
- 支援鍵盤操作 (Tab / Enter / Space)。
- 顏色對比度符合 WCAG AA 標準 (4.5:1 以上)。
