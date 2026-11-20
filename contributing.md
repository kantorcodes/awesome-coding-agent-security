# Contribution Guidelines

Thank you for helping keep coding agents safe. This list stays useful only if every line can be trusted.

## Entry criteria

An entry is accepted only if **all** of these are true:

1. **It is about coding agents or MCP.** Claude Code, Cursor, GitHub Copilot, OpenAI Codex, Gemini CLI, Windsurf, Cline, Roo Code, Kiro, Amazon Q, other agents that read a repository and run commands, or MCP servers and clients. General LLM safety and jailbreaks belong in the [related lists](README.md#related-lists).
2. **It links to a primary source.** A vendor advisory (GHSA, security bulletin), the NVD record, the original research post or paper, or the official documentation. News articles and aggregators are not accepted when the primary source exists.
3. **Every CVE number is checked on [NVD](https://nvd.nist.gov/)** and matches the product. Say in the pull request that you checked it.
4. **Facts come from the source.** Dates, versions and numbers are copied from the linked page, not from memory.
5. **Tools are maintained and open source**, with documentation, and are not archived. Say in the pull request if you are the author.
6. **No victim is named.** Never name a person or an organization whose repository was compromised, and never link to live malicious repositories or payloads.
7. **No marketing.** One objective sentence; no "best", "ultimate" or "revolutionary".

## Format

```md
- [Name](https://link) - Description that starts with a capital letter and ends with a period.
```

- One entry per pull request when possible.
- Add the entry at the bottom of the right section.
- Run `npx awesome-lint` locally; the CI also checks every link with lychee.

## Removal

Open an issue with the "Dead link or wrong fact" template. Entries whose source disappears, or whose facts turn out wrong, are removed or fixed.
