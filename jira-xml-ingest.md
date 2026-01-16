---
name: jira-xml-ingest
description: Ingest and interpret Jira ticket exports in XML (RSS) format; extract key fields and produce a clean summary + next steps.
---

# Jira XML (RSS) Ingest

Interpret Jira issues exported as XML in the RSS format (like the "XML representation of an issue" export).

## When to Use

- When a Jira ticket is pasted/exported as XML (RSS) and you want a clean, human-readable summary.
- When you want to turn a Jira XML export into structured data you can paste into notes, PR descriptions, or commit messages.

## Instructions

### Input expectations

- The input is typically an RSS document: `<rss><channel>...<item>...</item></channel></rss>`.
- If multiple `<item>` entries exist, process each and output a list.

### Parsing & normalization rules

- Treat the `<item>` as the primary unit (one issue).
- Extract text content; ignore attributes unless they carry IDs you want to keep (e.g., `<key id="...">`).
- The `<description>` field commonly contains HTML. Convert it to readable plain text:
  - Preserve code/pre blocks as fenced code blocks.
  - Convert inline code (e.g., `<tt>`) to backticks.
  - Collapse excessive whitespace.

### Fields to extract (preferred)

- **Key**: `<key>` (e.g., `PF-577`)
- **Title**: `<title>` (often includes `[KEY] Summary`)
- **Summary**: `<summary>`
- **Link**: `<link>`
- **Project**: `<project key="...">` and project name text
- **Type**: `<type>` text
- **Priority**: `<priority>` text
- **Status**: `<status>` text
- **Assignee / Reporter**: `<assignee>` / `<reporter>` text (keep `accountid` if present)
- **Created / Updated**: `<created>` / `<updated>` (keep original timezone string)
- **Resolution**: `<resolution>` (e.g., `Unresolved`)
- **Labels / Attachments / Subtasks**: include as empty/none if blank
- **Custom fields**: extract as a nested list of `{id, name, values[]}` where possible

### Output format

Produce, in order:

1. A short **one-paragraph summary** of what the ticket is asking for.
2. A **bullet list** of extracted fields (Key, Summary, Status, Priority, Assignee, Link, Created/Updated).
3. A **“What I’d do next”** section with 3–7 concrete next steps / questions, based on the description.
4. (Optional) A **machine-readable block** (YAML or JSON) containing the extracted fields.


