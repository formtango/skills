---
name: debug-a-form
description: Diagnose a website form that is not sending, not arriving, missing fields, or returning errors (404, 403, 429, CORS, redirect or spam problems). Use when a contact form "does nothing", submissions are missing, emails are not arriving, or a FormTango form returns an error.
---

# Debugging a form

Work through these in order. Most broken forms fail one of the first four.

## 1. Read the form tag

- `action` must be a full `https://` address. No action means the form posts to its own page, which on a static site goes nowhere.
- `method` must be `POST`. Without it the browser uses GET and puts the data in the address bar.
- An `action` of `mailto:` does not send a form; it opens an email program.
- A placeholder such as `YOUR_FORM_ID` left in the address returns 404.

## 2. Check every field has a `name`

Browsers only send named fields. An input with only an `id` or `placeholder` is silently left out. Two fields with the same name overwrite each other (radio buttons and checkboxes excepted).

## 3. Send a test and read the answer

```bash
curl -s -X POST https://formtango.com/f/FORM_ID \
  -H 'Content-Type: application/json' -H 'Accept: application/json' \
  -d '{"email":"test@example.com","message":"test","_test":"1"}'
```

| Answer | Meaning | Fix |
| --- | --- | --- |
| `{"ok": true}` | The endpoint works | The problem is in the page, not the endpoint |
| 404 | Unknown form id | Copy the endpoint again from the form's page |
| 403 | The form is paused | Turn it back on in its settings |
| 429 | Too many requests, or the monthly limit | Wait a minute, use `_test=1` while testing, or upgrade |
| 400 | The body could not be read, or the spam check was not completed | Send a normal form post or JSON; complete the CAPTCHA if one is set |

`_test=1` marks a test: stored for 24 hours, not counted, nobody emailed.

## 4. JavaScript submissions

- Pass the form element: `new FormData(form)`. Building the body by hand is where fields get lost.
- Send `Accept: application/json` to get JSON instead of a redirect.
- Do not set `Content-Type` when sending `FormData`.
- A CORS error almost always means a wrong URL or `http://`. The form endpoint accepts any origin, unless the owner listed allowed sites; then the page's site must be on that list.

## 5. It sends, but nothing appears

- Look in the form's **Spam** view. Automated tests are often flagged for being sent faster than a person could type; use `_test=1`.
- The hidden `_gotcha` field must stay empty. Add `autocomplete="off"` and `tabindex="-1"` so autofill does not fill it.
- Over the monthly limit, submissions are held, not shown. The dashboard says so.

## 6. No email

- Check the spam folder; mail comes from `no-reply@formtango.com`.
- Notifications must be on and set to "Right away", not a digest.
- Extra recipients must confirm their address first.
- Test submissions never send email.

## 7. Files and redirects

- File inputs need `enctype="multipart/form-data"` and a paid plan; 5 files and 4 MB per submission.
- Redirecting to your own thank-you page needs a paid plan and a full `https://` address.

More: https://formtango.com/docs/troubleshooting · Paste the form into https://formtango.com/tools/form-checker
