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
transition: fade-out
---

# 什麼是 MJML？

**Mailjet Markup Language**

<v-clicks>

- 由 **Mailjet** 開發的開源郵件標記語言
- 解決 **跨郵件客戶端兼容性** 的核心問題
- 使用 **語義化組件** 來構建響應式郵件
- 編譯時轉換為高兼容性的 HTML

</v-clicks>

<div v-click>

```xml
<mjml>
  <mj-body>
    <mj-section>
      <mj-column>
        <mj-text>Hello World!</mj-text>
      </mj-column>
    </mj-section>
  </mj-body>
</mjml>
```

</div>

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

# 為什麼需要 MJML？

## 郵件客戶端的挑戰

<div grid="~ cols-2 gap-4">
<div>

### 各客戶端限制
- **Outlook**：使用 Word 渲染引擎，CSS 支援有限
- **Gmail**：過濾大部分 CSS，不支援媒體查詢
- **Apple Mail**：WebKit 引擎，支援度較好但仍有限制
- **行動裝置**：螢幕尺寸差異，觸控操作考量

</div>
<div>

### 傳統 vs MJML

<div class="grid grid-cols-1 gap-4">

**❌ 傳統 HTML 郵件痛點**
- 需要使用 table 佈局
- CSS 內聯化繁瑣
- 跨平台測試耗時
- 響應式設計困難

**✅ MJML 解決方案**
- 語義化組件抽象
- 自動 CSS 處理
- 內建跨平台兼容
- 響應式預設支援

</div>
</div>
</div>

---

# MJML 技術架構

<div class="flex justify-center mb-8">
  <div class="bg-gray-100 p-4 rounded-lg">
    <h3 class="text-center mb-4">編譯流程</h3>
    <div class="text-center">
      MJML → AST Parser → Component Tree → HTML Generator → Optimized HTML
    </div>
  </div>
</div>

## 核心技術特點

<v-clicks>

- **AST 解析**：將 MJML 語法樹轉換為組件樹
- **組件系統**：可組合、可擴展的組件架構
- **CSS 內聯化**：自動處理樣式兼容性
- **響應式策略**：結合 media query 和 fluid layout

</v-clicks>

<div v-click>

```javascript
// MJML 編譯 API
const mjml = require('mjml');

const { html, errors } = mjml(mjmlString, {
  minify: true,
  keepComments: false,
  filePath: './templates/'
});
```

</div>

---

# 開發環境整合

## CLI 工具

```bash
# 安裝
npm install -g mjml

# 基本使用
mjml template.mjml -o output.html

# 監聽模式
mjml --watch src/ -o dist/

# 配置選項
mjml --config.minify true --config.beautify false
```

<v-click>

## Webpack 整合

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  module: {
    rules: [
      {
        test: /\.mjml$/,
        use: [
          {
            loader: 'mjml-loader',
            options: {
              minify: true,
              validationLevel: 'soft'
            }
          }
        ]
      }
    ]
  }
};
```

</v-click>

---

# 自定義組件開發

```javascript {all|1-3|5-10|12-17|19-24|26-35}
const { BodyComponent } = require('mjml-core');

class MjCustomButton extends BodyComponent {
  static componentName = 'mj-custom-button';

  static allowedAttributes = {
    'bg-color': 'color',
    'border-radius': 'unit(px,%)',
    'size': 'enum(small,medium,large)'
  };

  static defaultAttributes = {
    'bg-color': '#3498db',
    'border-radius': '4px',
    'size': 'medium'
  };

  getStyles() {
    return {
      table: {
        'background-color': this.getAttribute('bg-color'),
        'border-radius': this.getAttribute('border-radius')
      }
    };
  }

  render() {
    return this.renderMJML(`
      <mj-column>
        <mj-button ${this.htmlAttributes({
          'background-color': this.getAttribute('bg-color'),
          'border-radius': this.getAttribute('border-radius')
        })}>
          ${this.getContent()}
        </mj-button>
      </mj-column>
    `);
  }
}

module.exports = MjCustomButton;
```

---

# 性能優化策略

## 編譯時優化

```javascript
const mjml2html = require('mjml');

// 優化配置
const optimizedConfig = {
  minify: true,
  keepComments: false,
  fonts: {
    'Open Sans': 'https://fonts.googleapis.com/css?family=Open+Sans'
  },
  preprocessors: [
    // 移除多餘空白
    (xml) => xml.replace(/\s+/g, ' '),
    // 移除註解
    (xml) => xml.replace(/<!--[\s\S]*?-->/g, '')
  ]
};

const result = mjml2html(mjmlString, optimizedConfig);
```

<v-click>

## 快取策略

- **Template 快取**：編譯結果快取避免重複計算
- **Asset 優化**：圖片 CDN、Base64 內嵌決策
- **Bundle 分析**：追蹤組件依賴，優化打包大小

</v-click>

---

# CI/CD 整合實務

## GitHub Actions 範例

```yaml
name: Build Email Templates

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2

    - name: Setup Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '16'

    - name: Install dependencies
      run: npm ci

    - name: Build MJML templates
      run: npm run build:mjml

    - name: Run email compatibility tests
      run: npm run test:email-compatibility

    - name: Deploy templates
      run: npm run deploy:templates
```

<v-click>

## 測試策略

- **語法驗證**：MJML validator integration
- **渲染測試**：自動化截圖比對
- **兼容性測試**：Litmus/Email on Acid API

</v-click>

---

# 兼容性技術深入

| 客戶端 | 渲染引擎 | CSS 支援 | 特殊處理 |
|--------|----------|----------|----------|
| Outlook 2016+ | Word Engine | 極度有限 | MSO Conditional Comments |
| Gmail | 自訂引擎 | 過濾大部分 CSS | 內聯樣式 + 特殊 class |
| Apple Mail | WebKit | 支援度最好 | 媒體查詢支援 |
| Outlook.com | 自訂引擎 | 部分支援 | 特殊 meta tag |

<v-click>

```html
<!-- Outlook 條件渲染 -->
<!--[if mso]>
  <table role="presentation"><tr><td>
<![endif]-->
  <div style="display:inline-block;">
    Content for modern clients
  </div>
<!--[if mso]>
  </td></tr></table>
<![endif]-->
```

</v-click>

---

# 監控與除錯

## 開發時除錯

```javascript
const mjmlValidator = require('mjml-validator');

// 驗證 MJML 語法
const errors = mjmlValidator(mjmlString);
if (errors.length > 0) {
  console.error('MJML validation errors:', errors);
  errors.forEach(error => {
    console.log(`Line ${error.line}: ${error.message}`);
  });
}

// 編譯時錯誤處理
const { html, errors: compileErrors } = mjml(mjmlString, {
  validationLevel: 'strict'
});

if (compileErrors.length > 0) {
  console.warn('Compilation warnings:', compileErrors);
}
```

<v-click>

## 生產環境監控

- **渲染成功率**：追蹤各客戶端渲染狀況
- **載入時間**：監控郵件開啟速度
- **點擊熱圖**：分析用戶互動行為
- **錯誤追蹤**：Sentry 等工具整合

</v-click>

---

# 實際案例分析

## 電商促銷郵件架構

```xml
<mjml>
  <mj-head>
    <mj-attributes>
      <mj-button background-color="#ff6b6b" border-radius="8px" />
      <mj-section background-color="#ffffff" padding="20px" />
    </mj-attributes>
  </mj-head>

  <mj-body>
    <!-- Header -->
    <mj-section background-color="#2c3e50">
      <mj-column>
        <mj-image src="logo.png" width="150px" />
      </mj-column>
    </mj-section>

    <!-- Hero Section -->
    <mj-section background-url="hero-bg.jpg">
      <mj-column>
        <mj-text font-size="32px" color="#ffffff">
          週年慶大特價
        </mj-text>
        <mj-button href="https://example.com/sale">
          立即搶購
        </mj-button>
      </mj-column>
    </mj-section>
  </mj-body>
</mjml>
```

<v-click>

## 效益對比

- **開發時間**：從 2-3 天縮短到 0.5-1 天
- **兼容性測試**：從 1 天縮短到 2 小時
- **維護成本**：降低 60% 的修改時間

</v-click>

---

# 最佳實踐

## 架構設計原則

<v-clicks>

- **組件化思維**：建立可重用的組件庫
- **模板繼承**：使用 base template + partial
- **設計系統**：統一的顏色、字型、間距規範
- **響應式優先**：mobile-first 設計策略

</v-clicks>

<div grid="~ cols-2 gap-4" v-click>
<div>

### ❌ 常見錯誤
- 過度依賴複雜佈局
- 忽略圖片 alt 屬性
- 未考慮暗色模式
- 按鈕點擊區域過小

</div>
<div>

### ✅ 解決方案
- 保持簡潔的結構
- 完整的無障礙支援
- 適配暗色模式樣式
- 44px 最小點擊區域

</div>
</div>

---

# 技術選型比較

| 方案 | 學習曲線 | 兼容性 | 靈活度 | 生態系統 |
|------|----------|--------|--------|----------|
| MJML | 中等 | 優秀 | 中等 | 豐富 |
| Foundation for Emails | 較高 | 優秀 | 高 | 成熟 |
| React Email | 低（對 React 開發者） | 良好 | 高 | 新興 |
| 純 HTML | 高 | 完全掌控 | 完全 | N/A |

<v-click>

## 選擇建議

- **團隊技術棧**：考慮現有技能和工具鏈
- **專案需求**：複雜度和客製化程度
- **維護資源**：長期維護和更新成本
- **效能要求**：編譯速度和輸出品質

</v-click>

---
layout: center
class: text-center
---

# Q&A 環節

<div class="text-6xl text-blue-400 mb-8">
  🤔
</div>

## 有任何問題嗎？

讓我們一起討論 MJML 的技術細節

<v-click>

### 常見問題預告：

- 如何處理複雜的客製化需求？
- MJML 的效能瓶頸在哪裡？
- 如何整合現有的郵件發送系統？
- 團隊採用的學習成本評估？
- 與設計師的協作流程？

</v-click>

---
layout: two-cols
---

# 總結

## MJML 的技術價值

### ✅ 技術優勢
- 大幅降低兼容性工作量
- 提升開發和維護效率
- 強化團隊協作體驗
- 豐富的生態系統支援

### ⚠️ 考慮因素
- 學習新的語法和概念
- 需要建立新的工作流程
- 客製化能力有一定限制
- 依賴第三方維護

::right::

## 導入建議

<v-clicks>

- **試驗專案**：從小規模專案開始驗證
- **團隊培訓**：安排學習時程和知識分享
- **工具整合**：逐步完善開發和部署流程
- **效益評估**：建立量化指標追蹤改善效果

</v-clicks>

<div v-click class="mt-8 p-4 bg-blue-500 text-white rounded-lg text-center">

### 謝謝聆聽！

讓我們開始使用 MJML 來改善我們的郵件開發體驗吧！

</div>

---
layout: center
class: text-center
---

# 感謝大家！

<div class="text-4xl mb-4">
  📧 ✨
</div>

[GitHub - MJML](https://github.com/mjmlio/mjml) · [官方文檔](https://mjml.io/documentation/) · [線上編輯器](https://mjml.io/try-it-live)
