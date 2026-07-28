---
name: manual-devlog
description: Create detailed, review-ready development-log Markdown files, in Korean by default, from the current AI session, user-provided notes, and safe local context. Use when the user asks to write, summarize, archive, or prepare a manual devlog/blog-post draft without GitHub collection, API upload, or automatic publishing. Save each draft outside docs as a dated file under a project-level devlogs folder.
---

# Manual Devlog

Create an editable Markdown draft only. Do not publish, call an upload API, collect from GitHub, or use stored external credentials.

## Workflow

1. Ground the draft in the current conversation, explicit user notes, and relevant local files the user has placed in scope. Do not invent work, results, incidents, or metrics. If an important fact is missing, either omit it or label it as a question for the author.
   - When the available evidence cannot support a meaningful log — for example the skill is invoked before any real work happened — do not pad the document or write filler. Say what is missing and ask the user what to record.
2. Treat project names, private URLs, credentials, customer information, absolute paths, and internal infrastructure details as non-public unless the user explicitly authorizes them for the document. Do not copy secrets even when authorized.
3. Resolve the output root before writing: use the nearest enclosing git repository root, or the current working directory when the project is not a git repository. Create `devlogs/` there if it does not exist; never place generated drafts in `docs/`.
4. Decide `.gitignore` handling explicitly, and default to leaving it alone:
   - The default is **not** to add `devlogs/` to `.gitignore`. Drafts stay tracked with the project.
   - The first time you create `devlogs/` in a repository, tell the user this default and offer the alternative in one sentence. Do not block on the answer; if the user does not choose, keep the default.
   - Add a `devlogs/` line to `.gitignore` only when the user explicitly asks for it. Never remove, reorder, or rewrite unrelated entries, and never create `.gitignore` for a project that has none unless the user asks.
   - Never commit or push the draft on your own.
5. Name the file exactly `YYYYMMDD_[N]_제목.md`:
   - Determine the current date from environment context or a shell command. Never recall it from memory. Use the user's local timezone unless a date is specified.
   - List existing files beginning with that date and choose the next positive `N` (`[1]`, then `[2]`, etc.).
   - **Never overwrite an existing file.** If the target path already exists, raise `N` until the name is free.
   - Make `제목` a concise title of at most 40 characters. Replace filesystem-invalid characters (`\\ / : * ? " < > |`) with spaces and collapse repeated spaces.
6. Handle repeat runs so drafts do not duplicate each other:
   - When the date already has drafts, read them first. Write about the work they do not cover, and refer to them in one line instead of restating their content.
   - When the user asks to revise, extend, or correct a specific existing draft, edit that file in place instead of creating a new one.
7. Write a self-contained Markdown document with this required header format:

   ```markdown
   제목: 제목
   태그: 태그1, 태그2,

   # 대분류 제목

   ## 세부 항목

   본문 Markdown. 중요한 판단은 **강조**하고, 여러 항목은 목록으로 정리한다.
   ```

   The trailing comma after the last tag is intentional. Keep it, and keep the `제목:` / `태그:` keys exactly as written even when the body is in another language, unless the user asks otherwise.

   Each tag may contain **only Korean letters, Latin letters, and digits**. Hyphens, underscores, spaces, dots, and every other symbol are rejected by the publishing form. Join or shorten the word instead of separating it — write `ClaudeCode` or `스킬`, never `claude-code` or `agent skill`. Separate tags from each other with a comma and a space.

   Derive one or more concise tags from evidenced content. Do not add speculative tags. Use these body sections when supported by evidence, translated into the language of the body:
   - `# 오늘의 목표와 맥락`
   - `# 구현한 내용`
   - `# 설계와 판단`
   - `# 문제와 해결` (only when an actual problem is known)
   - `# 검증한 내용`
   - `# 다음 단계`
8. Structure the body with the Markdown in **Supported syntax** below, and follow the spacing rules there. Use `##` for meaningful subtopics, `###` only for a further level of detail, `-` or numbered lists for repeated items, `**강조**` for key decisions, and short code fences only when they clarify a decision. Do not make every paragraph a heading or use headings merely for visual size.
9. After writing, report the file path, selected sequence number, the `.gitignore` decision that was applied, and a short factual summary. State clearly that the file is a local draft and was not published.

## Supported syntax

The publishing form accepts exactly this Markdown. Stay inside the list, and do not emit raw HTML other than `<br>`.

| 용도 | 문법 |
| --- | --- |
| 제목 | `# 제목` |
| 굵게 | `**텍스트**` |
| 기울임 | `*텍스트*` |
| 취소선 | `~~텍스트~~` |
| 인라인 코드 | `` `code` `` |
| 코드 블록 | ` ```언어 ` |
| 인용문 | `> 인용문` |
| 순서 없는 목록 | `- 항목` |
| 순서 있는 목록 | `1. 항목` |
| 체크박스 | `- [ ] 항목` |
| 링크 | `[텍스트](URL)` |
| 이미지 | `![설명](경로)` |
| 표 | `\| 셀 \| 셀 \|` |
| 구분선 | `---` |
| 주석 | `<!-- 주석 -->` |
| 줄바꿈 | 공백 2개 또는 `<br>` |
| 각주 | `[^1]` |
| 수식 | `$수식$` |
| 이스케이프 | `\*` |

Line breaks decide whether the result is readable, so treat them as part of the writing:

- Put a blank line between every block — paragraphs, headings, lists, tables, quotes, and code fences. A heading or list glued to the previous paragraph reads as a wall of text.
- Keep a paragraph to two to four sentences carrying one idea. Split rather than extend.
- Use a blank line to separate thoughts. Reserve two trailing spaces or `<br>` for lines that must stay inside one paragraph, such as a short sequence or an aside.
- Do not leave more than one blank line in a row.

Reach for the element that matches the content instead of defaulting to paragraphs and bullets:

- A table when comparing two or more things across the same fields.
- A quote for citing an error message, a document, or the user's own words.
- A checkbox list for remaining work, and a plain list for finished items.
- A link whenever a repository, document, or command reference is named.
- A horizontal rule only to divide major parts of a long document.

Do not decorate for its own sake. No bold across a whole paragraph, no heading used as a font size, and no more than one idea per bullet.

## Style

- Write in Korean by default. When the user explicitly asks for another language, write the body and section titles in that language.
- Write clear prose; explain why decisions were made, not merely a list of changed files.
- Default to a substantial draft of roughly 1,500 to 3,000 characters across three to five top-level sections. Expand for “detailed” requests and compress when the user asks for something short.
- For detailed requests, connect implementation, tradeoffs, verification, and remaining work into a readable narrative.
- Keep source certainty visible: distinguish completed work from plans and assumptions.
