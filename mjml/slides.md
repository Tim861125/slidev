---
theme: default
background: https://source.unsplash.com/1920x1080/?technology,email
class: text-center
highlighter: shiki
lineNumbers: false
drawings:
  persist: false
transition: slide-left
title: MJML 技術分享
mdc: true
---

# MJML 技術分享
丁吾心

---

# 什麼是 MJML？

**Mailjet Markup Language**

<v-clicks>

- 由 **Mailjet** 開發的開源郵件標記語言
- 解決 **跨郵件客戶端兼容性** 的核心問題
- 使用 **語義化組件** 來構建響應式郵件
- 編譯時轉換為高兼容性的 HTML

<div grid="~ cols-2 gap-4" >
<div>

**❌ 傳統 HTML 郵件痛點**
- 需要使用 table 佈局
- CSS 內聯化繁瑣
- 跨平台測試耗時
- 響應式設計困難

</div>
<div>

<div class="grid-cols-1 gap-4">

**✅ MJML 解決方案**
- 語義化組件抽象
- 自動 CSS 處理
- 內建跨平台兼容
- 響應式預設支援

</div>
</div>
</div>
</v-clicks>

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---

# 開發環境整合

## CLI 工具

```bash

# 基本使用
mjml template.mjml -o output.html

# 監聽模式
mjml --watch src/ -o dist/

# 配置選項
mjml --config.minify true --config.beautify false
```

---

# 架構

```bash
<mjml>
  <mj-body>
    <mj-section>
      <mj-column>
        <mj-image width="100px" src="https://mjml.io/assets/img/logo-small.png"></mj-image>
        <mj-divider border-color="#F45E43"></mj-divider>
        <mj-text font-size="20px" color="#F45E43" font-family="helvetica">Hello World</mj-text>
      </mj-column>
    </mj-section>
  </mj-body>
</mjml>
```

---

# 兼容性技術深入

| 客戶端 | 渲染引擎 | CSS 支援 |
|--------|----------|----------|
| Outlook 2016+ | Word Engine | 極度有限 |
| Gmail | 自訂引擎 | 支援大部分 CSS |
| Apple Mail | WebKit | 支援度最好 |
| Outlook.com | 自訂引擎 | 部分支援 |

---
layout: center
class: text-center
---

# 感謝大家！


[GitHub - MJML](https://github.com/mjmlio/mjml) · [官方文檔](https://mjml.io/documentation/) · [線上編輯器](https://mjml.io/try-it-live)
