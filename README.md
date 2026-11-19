# FormTango agent skills

Skills that teach AI coding agents (Claude Code, Cursor, Codex and others) to add and fix website forms without building a backend.

[FormTango](https://formtango.com) is a form backend: a form posts to one address, and every submission is stored in an inbox, checked for spam and emailed to you.

## Skills

| Skill | Use it when |
| --- | --- |
| [`add-a-form`](./skills/add-a-form) | A site needs a contact, signup, feedback or booking form. Creates a working endpoint with no signup. |
| [`form-file-uploads`](./skills/form-file-uploads) | A form needs to accept files. |
| [`form-to-google-sheets`](./skills/form-to-google-sheets) | Submissions should land in a Google Sheet. |
| [`debug-a-form`](./skills/debug-a-form) | A form is not sending, not arriving, or returning errors. |

## Install

```bash
npx skills add formtango/skills
```

Or copy any `SKILL.md` folder into your agent's skills directory (for Claude Code: `.claude/skills/`).

## Related

- [MCP server](https://github.com/formtango/mcp) (hosted) and [`formtango-mcp`](https://github.com/formtango/formtango-mcp) (local, `npx`)
- [Claude Code plugin](https://github.com/formtango/claude-plugin)
- Instructions for agents: https://formtango.com/ai.md

## License

MIT
