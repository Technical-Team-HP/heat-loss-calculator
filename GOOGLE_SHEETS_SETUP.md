# Connecting the Heat Loss Calculator to Google Sheets

When **Calculate Heat Loss** is clicked the app sends the entered values to a Google Apps Script web app, which appends a row to a Google Sheet. Follow the steps below to set this up.

---

## 1. Create the Google Sheet

1. Go to [sheets.google.com](https://sheets.google.com) and create a new spreadsheet.
2. In the first row, add these column headings (order matters):

| A | B | C | D | E | F | G | H | I |
|---|---|---|---|---|---|---|---|---|
| Timestamp | Customer Name | Postcode | Floor Area (m²) | Property Era | Wall Type | Watts | kW | Status |

---

## 2. Add the Apps Script

1. In your Google Sheet, click **Extensions → Apps Script**.
2. Delete any default code and paste in the following:

```javascript
function doPost(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var data = e.parameter;

  sheet.appendRow([
    new Date(),
    data.customerName || '',
    data.postcode     || '',
    data.floorArea    || '',
    data.propertyEra  || '',
    data.wallType     || '',
    data.watts        || '',
    data.kw           || '',
    data.status       || ''
  ]);

  return ContentService.createTextOutput('OK');
}
```

3. Click **Save** (give the project any name you like).

---

## 3. Deploy as a Web App

1. Click **Deploy → New deployment**.
2. Click the gear icon next to **Select type** and choose **Web app**.
3. Set:
   - **Description:** Heat Loss Calculator Logger *(optional)*
   - **Execute as:** Me
   - **Who has access:** Anyone
4. Click **Deploy** and authorise the script when prompted.
5. Copy the **Web app URL** — it looks like:
   ```
   https://script.google.com/macros/s/AKfycb.../exec
   ```

---

## 4. Configure the Calculator

1. Open `index.html` in a text editor.
2. Near the top of the `<script>` block find:

```javascript
var APPS_SCRIPT_URL = 'YOUR_APPS_SCRIPT_WEB_APP_URL_HERE';
```

3. Replace `YOUR_APPS_SCRIPT_WEB_APP_URL_HERE` with the URL you copied:

```javascript
var APPS_SCRIPT_URL = 'https://script.google.com/macros/s/AKfycb.../exec';
```

4. Save and reload the page. Every time **Calculate Heat Loss** is clicked, a new row will be added to your sheet.

---

## Notes

- The request uses `mode: 'no-cors'` so the browser won't show any response — logging is fire-and-forget.
- If the URL is not configured (left as the placeholder), logging is silently skipped and a message is printed to the browser console.
- If you re-deploy the Apps Script after making changes, you will get a new URL — update `APPS_SCRIPT_URL` in `index.html` accordingly.
