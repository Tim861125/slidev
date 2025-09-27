---
theme: default
title: Google Chrome DevTools
info: Chrome 內建的開發者工具，前端工程師必備
---

# Google Chrome DevTools 
介紹篇

給網頁開發人員除錯與優化工具  

---
 
# 如何開啟 DevTools

- Chrome 主選單 → 更多工具 → 開發人員工具  
- 右鍵 → **Inspect**  
- 快捷鍵：  
  - Mac：`Command + Option + I`  
  - Windows/Linux：`F12` 或 `Ctrl + Shift + I`  

---

![alt text](/screenshot.png)

---

# DevTools 主要面板

- **Elements**：顯示 DOM 結構，查看與修改 HTML / CSS  
- **Console**：顯示錯誤與 log，支援執行 JS 指令  
- **Network**：Request / Response，分析 API  
- **Performance**：分析效能、渲染與記憶體  
- **Application**：查看 Storage、Cookies、Cache  
- **Security**：檢查 HTTPS / 混合內容問題

---

# 裝置模擬 (Device Mode)

- 模擬不同裝置螢幕尺寸與方向  
- 測試響應式設計 (Responsive)  
- 模擬地理定位、感測器（加速度計等）  

---

# 小結

- DevTools 為 Chrome 內建，無需額外安裝  
- 幾乎所有前端開發流程都會用到  
- 建議熟練四大面板：  
  - Elements  
  - Console  
  - Network  
  - Performance  
- 定期用 DevTools 檢查錯誤與效能 → 專業開發者習慣  

---
layout: cover
---

# Google Chrome DevTools 
Element 篇

---

# 查看與修改 HTML

- 直接在 Elements 面板點擊 DOM 節點
- 即時編輯 HTML 標籤、屬性與內容
- 拖曳 DOM 節點改變結構
- `Delete` 鍵可刪除節點

---

# 查看與修改 CSS

- **Styles** 窗格：顯示選定元素的 CSS 規則
- 即時新增、修改、停用 CSS 屬性
- 查看 CSS 盒模型 (Box Model)
- 強制設定元素狀態 (e.g. `:hover`, `:focus`)
- `.cls` 按鈕可以新增 class

<br>
<div>
  <button class="px-4 py-2 rounded-lg bg-blue-600 text-white hover:bg-yellow-700">
    Hover 我
  </button>
</div>

---

# DOM 中斷點 (DOM Breakpoints)

在特定 DOM 變動時暫停 JavaScript 執行

右鍵點擊元素 → **Break on...**
- **subtree modifications**: 子節點變動
- **attribute modifications**: 屬性變更
- **node removal**: 節點被移除

→ 有效追蹤動態變更 DOM 的程式碼

---

# 小技巧

- **$0**: 在 Console 中代表當前選中的元素
- **H**: 隱藏元素 (同等於 `visibility: hidden`)
- **右鍵 → Scroll into view**: 將頁面滾動至元素位置
- **右鍵 → Capture node screenshot**: 擷取單一元素的截圖