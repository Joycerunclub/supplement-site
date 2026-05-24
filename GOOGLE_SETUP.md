# 📊 Google 試算表設定教學

訂單送出後自動寫入你的 Google 試算表，只需要設定一次！

---

## 第一步：建立 Google 試算表

1. 打開 [Google 試算表](https://sheets.google.com)
2. 新增一個空白試算表
3. 把第一行改成這些標題（A1 到 I1）：

| A | B | C | D | E | F | G | H | I |
|---|---|---|---|---|---|---|---|---|
| 時間 | 姓名 | 電話 | LINE | 地址 | 訂購內容 | 合計 | 備註 | 狀態 |

4. 記下這個試算表的網址

---

## 第二步：建立 Google Apps Script

1. 在試算表上方選單點 **擴充功能 → Apps Script**
2. 把預設的程式碼**全部刪掉**
3. 貼上以下程式碼：

```javascript
function doPost(e) {
  try {
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    const data = JSON.parse(e.postData.contents);
    
    sheet.appendRow([
      data.timestamp,
      data.name,
      data.phone,
      data.line || '',
      data.address,
      data.items,
      data.total,
      data.note || '',
      '待確認'  // 狀態預設為待確認
    ]);
    
    return ContentService
      .createTextOutput(JSON.stringify({ result: 'success' }))
      .setMimeType(ContentService.MimeType.JSON);
  } catch(error) {
    return ContentService
      .createTextOutput(JSON.stringify({ result: 'error', error: error.toString() }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

4. 按 **Ctrl + S** 存檔，名稱可以填「補給站訂單接收」

---

## 第三步：部署 Web App

1. 右上角點 **部署 → 新增部署作業**
2. 類型選 **網頁應用程式**
3. 設定如下：
   - 說明：補給站訂單
   - 以誰的身分執行：**我（你的 Google 帳號）**
   - 誰可以存取：**所有人**
4. 點 **部署**
5. 系統會要求授權，點 **授予存取權**
6. 複製產生的 **網頁應用程式網址**（長這樣：https://script.google.com/macros/s/XXXXX/exec）

---

## 第四步：填入網址

打開 `order.html`，找到這一行：

```javascript
const GOOGLE_SCRIPT_URL = 'YOUR_GOOGLE_SCRIPT_URL_HERE';
```

把 `YOUR_GOOGLE_SCRIPT_URL_HERE` 換成你剛剛複製的網址，例如：

```javascript
const GOOGLE_SCRIPT_URL = 'https://script.google.com/macros/s/AKfycb.../exec';
```

存檔後重新上傳到 GitHub，就完成了！

---

## 完成後的效果

學員送出訂單後，你的 Google 試算表會自動新增一行：

| 時間 | 姓名 | 電話 | LINE | 地址 | 訂購內容 | 合計 | 備註 | 狀態 |
|------|------|------|------|------|---------|------|------|------|
| 2025/1/1 | 小明 | 0912... | abc | 台北市... | 太空能量膠 ×2... | $1,200 | | 待確認 |

確認付款後，你可以手動把「狀態」改成「已確認」或「已出貨」。

---

## 有任何問題

把截圖給 Claude 看，繼續請他幫你解決！
