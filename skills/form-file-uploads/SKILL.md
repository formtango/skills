---
name: form-file-uploads
description: Add a file upload (CV, photo, document, attachment) to a website form without building storage or a server, using FormTango. Use when a form needs to accept files, when uploaded files are not arriving, or when only the file name arrives instead of the file.
---

# File uploads on a form

A file input only sends the file if the form is multipart. With FormTango the file is stored with the submission; there is no storage to set up.

## The form

```html
<form action="https://formtango.com/f/FORM_ID" method="POST" enctype="multipart/form-data">
  <input type="email" name="email" required>
  <input type="file" name="attachment" accept=".pdf,.doc,.docx,.png,.jpg" required>
  <input type="text" name="_gotcha" style="display:none" tabindex="-1" autocomplete="off">
  <button type="submit">Send</button>
</form>
```

## With JavaScript

```js
await fetch('https://formtango.com/f/FORM_ID', {
  method: 'POST',
  headers: { Accept: 'application/json' },
  body: new FormData(form), // do NOT set Content-Type yourself
});
```

## Rules

- `enctype="multipart/form-data"` is required. Without it only the file name is sent.
- Never set the `Content-Type` header by hand when sending `FormData`; the browser adds the boundary.
- Limits: up to 5 files and 4 MB in total per submission. Common document, image, audio and video types are accepted.
- File uploads need a paid FormTango plan. On the free plan the rest of the submission is saved and the files are left out. Tell the user this.
- `accept` only filters the file picker; types are checked again on the server.

## If it is not working

1. Check the form tag has `enctype="multipart/form-data"`.
2. Check the input has a `name`.
3. Check the total size is under 4 MB.
4. Send a test with `_test=1` and look at the response: `{"ok": true}` means it arrived.
