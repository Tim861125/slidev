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
- 強制設定元素狀態 (e.g. `:hover`)
- `.cls` 按鈕可以新增 class

<br>
<div>
  <button class="px-4 py-2 rounded-lg bg-blue-600 text-white hover:bg-yellow-700">
    按鈕
  </button>
</div>

---

# DOM 中斷點 (DOM Breakpoints)

<div style="display: flex; gap: 50px; align-items: flex-start;">

<!-- 左側文字說明 -->
<div style="flex: 1;">
  <ul>
    <li>在特定 DOM 變動時暫停 JavaScript 執行</li>
    <li>右鍵點擊元素 → <b>Break on...</b>
      <ul>
        <li><b>subtree modifications</b>: 子節點變動</li>
        <li><b>attribute modifications</b>: 屬性變更</li>
        <li><b>node removal</b>: 節點被移除</li>
      </ul>
    </li>
  </ul>
  <p>→ 有效追蹤動態變更 DOM 的程式碼</p>
</div>


<!-- 右側示範按鈕 -->
<div style="flex: 1;">
  <button 
    style="padding: 12px 24px; border-radius: 6px; background-color: #60a5fa; color: white; cursor: pointer;"
    onclick="this.innerText='點擊!'"
  >
    示範按鈕
  </button>
</div>

</div>

---
layout: cover
---

# Google Chrome DevTools 
Console 篇

---

# Console 面板功能

- 顯示網頁執行過程中的錯誤、警告與訊息  
- 可輸入並執行 JavaScript 指令  
- 支援即時測試 API / DOM 操作  
- 與 Elements、Network 等面板整合，快速取值  

---

# 常用 Console 指令

- `console.log()`：輸出一般訊息  
- `console.warn()`：黃色警告  
- `console.error()`：紅色錯誤訊息  
- `console.table()`：以表格方式顯示陣列 / 物件  
- `console.dir()`：以物件樹方式顯示 DOM  

---

# 除錯技巧

- 查看變數：直接輸入變數名稱  
- 使用上下鍵切換輸入歷史  
- `_` 代表上一次執行結果  
- `copy(value)`：快速複製結果到剪貼簿  
- `$0`：代表 Elements 面板目前選取的元素  
- `$1, $2...`：前幾個選取過的元素  

---

# 小技巧

- `clear()`：清空 Console  
- `filter` 搜尋輸出內容  
- 搭配 `console.group()` 與 `console.groupEnd()` 整理輸出  
- 使用 `console.time()` 與 `console.timeEnd()` 測量程式耗時  
- 方便進行 **效能檢測** 與 **資料比對**  

---

# 小結

- Console = 除錯 & 測試 JS 的最佳工具  
- 開發過程中，記錄錯誤 / 資料檢查必備  
- 熟悉 `console.log / warn / error / table` → 提升除錯效率  
- 靈活搭配 `$0`、`copy()` 等指令，加快開發流程  

---
