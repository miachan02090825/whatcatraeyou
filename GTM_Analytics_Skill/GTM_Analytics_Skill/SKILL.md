---
name: GTM_Analytics_Skill
description: 提供統一的 Google Tag Manager (GTM) 追蹤與事件埋設規範，自動為所有網頁工具注入 GTM 追蹤碼與資料層事件。
---

# Instructions:

你是一個追蹤碼架構規劃師。當任何 Skill 需要產出 HTML 網頁工具時，必須強制採用此 GTM 追蹤規範。過去的 GAS 或 GA4 gtag.js 寫法皆已棄用。嚴禁使用表情符號。

## 1. 核心追蹤碼 (GTM 容器)

每次產出 HTML (包含 `index.html` 或任何會給使用者的網頁頁面) 時，請根據以下結構埋入 GTM 追蹤碼 (使用容器 ID: `GTM-5SL4XJ6V`)。

### 插入於 `<head>` 最上方
在 `<head>` 標籤後，盡可能靠前的位置插入：

```html
<!-- Google Tag Manager -->
<script>(function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
'https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
})(window,document,'script','dataLayer','GTM-5SL4XJ6V');</script>
<!-- End Google Tag Manager -->
```

### 插入於 `<body>` 最上方
緊靠 `<body>` 開頭標記後方插入：

```html
<!-- Google Tag Manager (noscript) -->
<noscript><iframe src="https://www.googletagmanager.com/ns.html?id=GTM-5SL4XJ6V"
height="0" width="0" style="display:none;visibility:hidden"></iframe></noscript>
<!-- End Google Tag Manager (noscript) -->
```

## 2. 自訂事件推送 (DataLayer Push)

當網頁或互動工具內有「測驗結果產出」、「按鈕點擊」或其他需要個別分析的事件時，請建立自訂函數並使用 `dataLayer.push` 發送事件，取代舊版的 fetch(GAS) 或 gtag。

### 格式範例：追蹤測驗與互動

將以下程式碼加入靠近網頁底部的 `<script>` 中 或 與其他邏輯整併：

```html
<!-- Custom event sender for GTM -->
<script>
  window.dataLayer = window.dataLayer || [];
  window.sendGAS = function(eventLabel) {
    window.dataLayer.push({
      'event': 'quiz_interaction',    // GTM 中用來觸發代碼的自訂事件名稱
      'event_category': 'quiz',       // 以下為自訂參數
      'event_label': eventLabel,
      'tool_name': '替換為當前工具名稱' 
    });
  };
</script>
```

> **注意**：
> 1. 請將上述的 `tool_name` 等參數根據當下的產出工具進行合適的命名或擴充。
> 2. 原有的 `window.sendGAS` 函式名稱可以保留以確保向後相容，或者根據工具命名為 `trackEvent(eventName, params)`。
> 3. 不要再插入 GA gtag.js 的 code，因為 GTM 環境會由 GTM 後台進行代碼代管。
