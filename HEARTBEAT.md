# HEARTBEAT.md

# Keep this file empty (or with only comments) to skip heartbeat API calls.

# Add tasks below when you want the agent to check something periodically.


# FounderClaw Heartbeat Protocol

On each heartbeat, check timestamps in activity_log.md and execute whichever 
tasks are due.

## Task Schedule

### SCOUT — Every 6 hours
Last run tracked via: `[SCOUT]` entries in activity_log.md

If 6+ hours since last Scout run:

1. Read research_sources.md for the monitoring list
2. Determine which sources are due:
   - Tier 1: every run (6hr)
   - Tier 2: daily (check last_checked)
   - Tier 3: every 2-3 days (check last_checked)
3. For each due source:
   a. Open in browser
   b. Scan posts from the relevant time window (6-12hr for Tier 1, 24-72hr for Tier 2/3)
   c. Look for frustration signals using patterns in AGENTS.md
   d. For each signal: record in the format specified in AGENTS.md
   e. Deduplication check against cases_index.md before saving
4. Append new signals to problems_raw.md
5. Update last_checked timestamps in research_sources.md
6. Log: `[timestamp] SCOUT: Checked [N] sources. Found [N] signals ([N] high, [N] medium, [N] low). [N] matched existing cases. [N] new.`
7. If any HIGH signal found, send brief Telegram alert:
🔍 SCOUT: Found strong signal
"[snippet]" — u/[user] in [source]
[engagement stats]
Queued for scoring.

Cost budget: $0.30-0.60 per run. If a run exceeds 45 minutes of browsing, wrap up and note skipped sources.

Do NOT browse more than 20 sources per cycle.

### ANALYST — Every 12 hours
Last run tracked via: `[ANALYST]` entries in activity_log.md

If 12+ hours since last Analyst run:

1. Read problems_raw.md for entries with "Processed: NO"
2. For each unprocessed signal:
   - If matched to existing case → merge into that case's evidence.md, flag for re-scoring
   - If new → check once more against existing cases, then create new case folder
   - Mark signal as processed
3. For each case needing scoring (new, or has new evidence, or last scored >48hr ago):
   a. Read ALL evidence in the case
   b. Score all 6 dimensions with justifications (per AGENTS.md framework)
   c. Calculate weighted composite
   d. Run MANDATORY self-critique (4 review questions from AGENTS.md)
   e. Adjust if needed, assign confidence
   f. Update case_summary.md and cases_index.md
4. Threshold actions:
   - Score >= 65: Send Telegram alert (INTERESTING)
   - Score >= 75: Auto-trigger deep validation protocol (per AGENTS.md), send alert (RECOMMENDED)
   - Score >= 90: Immediate deep validation, alert (EXCEPTIONAL)
5. Log: `[timestamp] ANALYST: Processed [N] signals. Created [N] new cases. Scored [N] cases. Score changes: [details]. Alerts sent: [details].`

Cost budget: $0.50-1.50 without deep validation. $1.50-3.00 per case with deep validation.
If >2 cases qualify for deep validation simultaneously, alert human first.

### INBOX CHECK — Every 2 hours
Last run tracked via: `[INBOX]` entries in activity_log.md

If 2+ hours since last inbox check AND outreach messages have been sent:

1. Open Reddit in browser
2. Check inbox/messages for new replies
3. For each new reply:
   - Match to the outreach message it's responding to
   - Assess commitment signals
   - Generate 2-3 Mom Test follow-up suggestions
   - Send Telegram alert (RESPONSE RECEIVED format from AGENTS.md)
4. Log: `[timestamp] INBOX: Checked. [N] new replies.`

### DAILY SUMMARY — Every 24 hours
Last run tracked via: `[DAILY]` entries in activity_log.md

If 24+ hours since last daily summary:

1. Compile stats from activity_log.md for the last 24 hours
2. Send Telegram daily summary (format from AGENTS.md)
3. Log: `[timestamp] DAILY: Summary sent.`

## Heartbeat Decision Logic

On each heartbeat:
1. Read activity_log.md for last run timestamps of each task
2. Check which tasks are overdue
3. Execute overdue tasks in priority order: ANALYST first, then SCOUT, then INBOX, then DAILY
4. If no tasks are due: reply HEARTBEAT_OK

### Browser Usage
All browser interactions must use profile="browserless" to leverage the Browserless remote browser instance.

## Important

- If activity_log.md doesn't exist yet, create it and run all tasks
- If research_sources.md doesn't exist, alert the human: "I need seed interest areas to start scouting. Send me 2-3 topics."
- If any task fails (browser timeout, rate limit, etc.), log the failure and continue with next task
- Never skip the self-critique review in the Analyst task


### Interest Area Expansion
If during scouting you discover a cluster of 3+ strong signals in a 
topic area outside the current seed interests, propose it to the human 
via Telegram:

"🆕 NEW INTEREST AREA DETECTED
Topic: [description]
Evidence: [N] signals found in [sources]
Example: "[paraphrased frustration]"
Should I add this as a monitored area? YES / NO"

Do not wait for approval to capture the individual signals — log them 
in problems_raw.md regardless. The approval is about whether to 
actively monitor new communities in this area going forward.