---
name: mcp-ski-data-pull
description: The person's mcp.ski data pull and what it kept. Use for "is my data pull on", "turn on the data pull", "stop reading my email", "what did you pull from my email", "my to-dos", "what's on my list", "my items", "delete that item", "my tracker proposals", "what's waiting on the tracker". Not for snowmaking reports (that is save-snowmaking-report). Uses the mcp-ski tools only.
---

# MCP Ski data pull

mcp.ski reads each person's own sources overnight, on its own server, and keeps only short
work facts: to-dos, decisions, status. Each fact is tagged with a topic (todo, project,
capex, snowmaking, lift or other), a resort and a date. Nothing in this skill fetches mail;
the server does that. This skill lets the person check the pull, turn it off, and use what
it kept.

Everything here goes through the mcp-ski tools, as the person. Never fetch their mail
yourself to "help" the pull, and never send items in for them unless they ask.

## What is kept, and what is not
Say this, in your own short words, when they ask what the pull does:
- Each night it reads the last 2 days of their email, the Drive files they own or opened,
  their calendar for the next week, and their Fireflies meetings, for the sources they turned on.
- Only a title and the work facts are kept. The email, document or transcript text itself is never stored.
- Two automatic screens check everything first: a check on mcp.ski's own server, then a
  screening model. Anything about HR, pay, guests or personal matters is turned away and not kept.
- Promotions, social and newsletter mail are skipped.
- Guest details (names, contact details, pass or folio numbers) are never stored.
- Contact details and full names are masked before any AI reads the text.
- Who sees it: they and the MCP Ski admins. A fact only reaches a project or the tracker when they say yes.
- Turning a source off deletes everything kept from it. Facts stay until they delete them
  or turn the source off.

## "Is my data pull on?"
1. Call project_ops-details with section ros_ingest-get_my_consent.
2. Read back, one line each, which sources are on (Gmail, Calendar, Drive, meetings, Chat)
   and any exclude list ("mail from these senders or with these words is skipped").
3. Turning a source ON is limited to the MCP Ski admins while the pull is tested. If they
   want it on and it is off, tell them: "Turning it on is admin-only for now; ask an MCP Ski admin."
   Do not call ros_ingest-set_my_consent with allowed true for them. If the response's
   can_enable_pull is true, they are an admin: ask once ("mcp.ski will read your recent
   email each night and keep only work facts, never the email. Turn it on?") and only on a
   clear yes call ros_ingest-set_my_consent with {"source": "gmail", "allowed": true}.
   They also connect Google themselves at https://dashboard.mcp.ski/settings.
4. To skip part of a source, add words to its exclude list (a sender domain, a label, a
   meeting name): ros_ingest-set_my_consent with {"source": "gmail", "allowed": <its current
   value from step 1>, "exclude": [...]}. Changing the exclude list never turns a source on.

## "My items" / "What's on my list?"
1. Call project_ops-details, section ros_ingest-list_my_items, arguments
   {"limit": 200, "offset": 0} (add "source": "gmail" to see only email). If total is more
   than you have, call again with offset raised by 200 until you have them all.
2. From each item's tagged_facts, keep those with topic todo. Show them newest first, one
   line each: the fact, then the resort and date if set, then the item title in brackets.
3. For "what's going on at Purgatory" and the like, filter tagged_facts by resort_key
   instead, and group by topic.
4. Facts with no topic were sent before tagging existed; show them under "Other".

## My tracker proposals
Each night mcp.ski matches the person's new to-dos and project facts to the Strategic
Alignment tracker. When one fits a tracker item, it proposes a change for them, such as a
new next action or a new item. Nothing on the tracker moves until they say yes. Only they
see their proposals.

1. Call project_ops-details with section alignment-list_changes and arguments
   {"state": "proposed", "mine": true}.
2. If there are none, say "Nothing waiting for the tracker." and stop.
3. Otherwise list them, numbered, one line each: the tracker item, then old → new. Keep it to the first 10.
4. Ask once: "Apply all, some (give the numbers), or none?"
5. Apply only what they said yes to: call alignment-confirm_changes with {"change_ids": [...]}, at most 10 at a time.
6. Read back each result in one line: applied = done; duplicate = it was already there;
   conflict = someone changed it first (show both values and leave it as it is).

Never confirm without the person's yes in this chat. Never confirm someone else's proposal.
Never propose new changes yourself unless they ask.

## Turning it off, deleting
- Stop one source: ros_ingest-set_my_consent with {"source": "gmail", "allowed": false}.
  This also deletes what was already kept from it. Say so before you do it, and get a yes.
- Delete one item: ros_ingest-delete_my_item with its item_id. Its facts go too.
- What has been kept and refused: project_ops-details, section ros_ingest-get_my_summary.

## Rules
- Only the person's own items. Never ask about or show anyone else's.
- Never quote mail. The facts are already short; repeat them as they are.
- If a tool is missing or "not available", their mcp-ski sign-in needs a refresh: the
  "mcp-ski-skills" sign-in card in Grok, Disconnect then Connect.
