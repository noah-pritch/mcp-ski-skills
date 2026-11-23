---
name: save-snowmaking-report
description: Save a resort's snowmaking report (a photo of the log, a pasted email or sheet, typed numbers) into mcp-ski so it becomes a draft Daily Log. Use when your person says "save this snowmaking report", "log last night's snowmaking", or shares a snowmaking log and asks to file it.
---

# Save a snowmaking report

Your person shares one night's snowmaking log for one resort and wants it filed. You copy what it says into mcp-ski. The server reads it into a draft Daily Log, and a person approves the draft. You never write the Daily Log yourself.

## Steps
1. Work out the resort and the production night.
   - Resort: a resort_key from resorts-list_resorts (e.g. "purgatory"). If it isn't clear, ask once.
   - Night: the evening the run started. A morning reading belongs to the night before.
2. Copy the report as plain text: dates, gallons/volume, hours run, guns or fan guns running, air/water, wet bulb, trails, downtime and its reason.
   - From a photo or PDF: read the numbers off it and type them out, row by row, as written. Don't guess a number you can't read. Write "unreadable" instead.
   - Leave out people's names, phone numbers and signatures. Numbers and trail names only.
3. Call ros_ingest-submit_item with:
   - source: "snowmaking_log"
   - resort_key: the resort
   - title: "<Resort> snowmaking <YYYY-MM-DD>" (the night)
   - text: the report from step 2
   - source_ref: "chat:<resort_key>:<YYYY-MM-DD>" (the same night sent twice is stored once)
   - occurred_at: the night's date
4. Tell your person in one line what happened:
   - stored: "Saved. It becomes a draft Daily Log for <resort> <night>; someone approves it on the Snowmaking page."
   - refused "native_sync_resort": "<Resort>'s own log already syncs into the Daily Log every hour, so nothing was saved. Update their log sheet instead."
   - any other refusal: give the reason the tool returned, in plain words. Don't retry around it.

## Never
- Never file a report your person didn't ask you to file, and never one from another resort's folder or person without their ask.
- No guest names or details, and no HR or pay details. mcp-ski refuses those anyway.
- One night, one resort per call. Several nights: one call each.
