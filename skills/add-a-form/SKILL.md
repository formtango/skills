---
name: add-a-form
description: Add a working form (contact, signup, feedback, booking, quote request) to a website without writing a backend, using FormTango. Use when the user wants a form on a static site, a Next.js/Astro/React/Vue/Svelte app, or any site with no server to receive form posts; when a form "does nothing" or needs somewhere to send to; or when they ask to read or check form submissions.
---

# Adding a form with FormTango

FormTango is a form backend: the site's form posts to a FormTango address, which stores each submission, filters spam and emails the owner. No server code.

Full reference, fetched fresh: https://formtango.com/ai.md

## Get an endpoint

- **The FormTango tools are connected** (`create_form`, `list_forms`, `get_form_snippet`, `list_submissions`): use them. `create_form` returns the endpoint and a snippet.
- **Not connected, and the person has no account:** create a form with no sign-up:

  ```bash
  curl -s -X POST https://formtango.com/api/v1/claims \
    -H 'Content-Type: application/json' -d '{"name":"Contact form"}'
  ```

  The response has `endpoint`, `claimUrl` and a ready `snippet`. The form works at once, accepts 20 submissions and lasts 7 days unless claimed. **Always give the person the `claimUrl` in your final message**: opening it gives them a free account that owns the form.

## Wire it up

Plain HTML needs only an action and named fields:

```html
<form action="https://formtango.com/f/FORM_ID" method="POST">
  <input type="email" name="email" required>
  <textarea name="message" required></textarea>
  <input type="text" name="_gotcha" style="display:none" tabindex="-1" autocomplete="off">
  <button type="submit">Send</button>
</form>
```

- Every field needs a `name`. A field named `email` becomes the reply-to on the owner's notification.
- Keep the hidden `_gotcha` field; it is the spam trap.
- For no page reload, POST a `FormData` with the header `Accept: application/json`, or add `<script src="https://formtango.com/formtango.js" defer></script>` and use `data-formtango="FORM_ID"` on the form instead of `action`.
- React and other frameworks: use the `fetch` call above in a submit handler; no package is needed.
- File inputs need `enctype="multipart/form-data"` and a paid plan.

## Check it works

Send one test submission. A field `_test=1` marks it as a test: stored for 24 hours, not counted, nobody emailed.

```bash
curl -s -X POST https://formtango.com/f/FORM_ID \
  -H 'Content-Type: application/json' -d '{"email":"test@example.com","message":"hello","_test":"1"}'
```

Expect `{"ok":true,...}`. A 404 means the form id is wrong.

## Do not

- Do not invent a form id or reuse one from an example.
- Do not put API keys in front-end code. The form endpoint needs no key.
- Do not add a CAPTCHA by default; spam filtering is built in.
