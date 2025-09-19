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
  <mj-head>
    <mj-title>{{title}}</mj-title>
    <mj-preview>{{abstract}}</mj-preview>
    <mj-attributes>
      <mj-all font-family="Microsoft JhengHei, Arial, sans-serif"/>
    </mj-attributes>
  </mj-head>
  <mj-body>
    <mj-section>
      <mj-column>
        <mj-image width="100px" src="https://mjml.io/assets/img/logo-small.png"></mj-image>
        <mj-text font-size="20px" color="#F45E43" font-family="helvetica">Hello World</mj-text>
        <mj-button background-color="#F63A4D" href="#">Promotion</mj-button>
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

# 萬惡的 Outlook

###  可以正常用的

- **`<mj-section>` / `<mj-column>`**
  Outlook 用 table render，安全
- **`<mj-text>`**
  文字正常，但需要設定 `line-height`
- **`<mj-image>`**
  設固定寬度 + `alt`
- **`<mj-button>`**
  Outlook 會用 VML `<v:roundrect>` 模擬
- **`<mj-divider>`**
  OK，但 dotted / dashed 可能退化成實線

---

## 要注意的

1. **line-height**
   - Outlook `normal` ≠ Gmail/Apple Mail
   - 建議用 **px 值**

2. **border-radius**
   - Section / Column **不支援**

3. **background-image**
   - Outlook 桌機 **不支援 CSS 背景圖**

4. **position / flex / grid**
   - Outlook 全部不支援
   - 只能靠 table

---

# 要注意的

5. **padding / margin**
   - margin 無效
   - 用 `padding` 或 `<mj-spacer>`

6. **SVG**
   - Outlook 桌機 **不顯示**
   - 用 PNG

7. **min-height / max-width**
   - 不支援
   - 用 table 結構撐開

---

# 不能用的

- CSS animation / transition
- Web fonts (Google Fonts)
- GIF 播放控制 (只顯示第一幀)
- CSS media queries (桌機版不支援)

---

# note

- 測試平台必備：Litmus / Email on Acid

- 行距用 px 值，不用 normal

- 用 `<mj-spacer>` 取代 margin

- 避免 SVG / 背景圖 / 動畫

- 保持版型簡單，圖片用 PNG/JPG

---

# 🎯 結論

> MJML 可以大幅減少 email 排版痛苦
> 但 Outlook (Word engine) 仍是最大相容性挑戰
> 設計時要 **以最保守結構為主**，確保跨平台一致


---
layout: center
class: text-center
---

# 感謝大家！


[GitHub - MJML](https://github.com/mjmlio/mjml) · [官方文檔](https://mjml.io/documentation/) · [線上編輯器](https://mjml.io/try-it-live)
