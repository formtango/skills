---
name: form-to-google-sheets
description: Send website form submissions to a Google Sheet as new rows, using FormTango and a small Apps Script, with no third-party automation tool. Use when the user wants form responses, sign-ups or leads in Google Sheets.
---

# Form submissions into Google Sheets

FormTango can post each submission to a webhook. A Google Apps Script web app attached to the sheet receives it and appends a row. Nothing else is involved.

## Steps for the user

1. In the sheet: **Extensions → Apps Script**. Replace the contents with the script below.
2. **Deploy → New deployment → Web app**, with "Who has access" set to **Anyone**. Copy the web app URL.
3. In FormTango, open the form → **Integrations** → add a **Webhook** with that URL.
4. Send a test from FormTango, then submit the real form.

## The script

```js
function doPost(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheets()[0];
  var data = JSON.parse(e.postData.contents).data;
  var fields = data.fields;

  if (sheet.getLastRow() === 0) {
    sheet.appendRow(['Received'].concat(Object.keys(fields)));
  }
  var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0];

  Object.keys(fields).forEach(function (key) {
    if (headers.indexOf(key) === -1) {
      headers.push(key);
      sheet.getRange(1, headers.length).setValue(key);
    }
  });

  sheet.appendRow(headers.map(function (header) {
    return header === 'Received' ? data.createdAt : (fields[header] || '');
  }));
  return ContentService.createTextOutput('ok');
}
```

## Notes

- Webhooks are part of paid FormTango plans.
- The web app URL lets anyone who has it add rows. Treat it like a password: do not put it in front-end code or a public repository.
- New form fields get a new column automatically.
- After editing the script, deploy a **new version**, or the old code keeps running.
- Full guide: https://formtango.com/integrations/google-sheets
