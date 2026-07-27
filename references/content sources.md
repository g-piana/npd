- Trade press for your target domains (The Robot Report, sUAS News, PV Magazine, Rivista Energia, industrial IoT newsletters) — this is where you'll hear people complaining about problems
- The actual regulation texts, particularly the CRA and AI Act annexes — not summaries, but the specific articles that define what's required, because product ideas hide in the gap between "what the regulation requires" and "what tooling exists to do it"
- CORDIS (EU research project database) — scan active projects in energy, drones, robotics, security; the EU funds solutions to problems that are real and budgeted

### Stage 2c - Problem Finding

- **Hackaday blog + Stack** — hardware/edge practitioner pain, as you identified
- **r/embedded, r/robotics, r/drones, r/solar, r/FPGA** — subreddit complaint threads are gold; search for "frustrated", "workaround", "wish there was"
- **CORDIS** (cordis.europa.eu) — EU-funded research projects; the project descriptions literally say "the problem is X and no solution exists" — that's a validated problem statement with EU budget behind it
- **LinkedIn groups for target industries** — less useful for conversation, very useful for spotting what vendors are _failing_ to solve (look at the comments on product announcements)

#### RSS from ArXiv

Standard RSS Feed Suffixes

Make sure your base URL starts with **`https://rss.arxiv.org`**: [[1](https://blog.arxiv.org/2024/01/31/attention-arxiv-users-re-implemented-rss/)]

- For **`cs.LG`**, add this exact text to the end:  
    `/rss/cs.LG`
- For **`eess.SP`**, add this exact text to the end:  
    `/rss/eess.SP` [[1](https://info.arxiv.org/help/rss.html)]
Combined API Feed Suffix

If you want both topics inside a single timeline, your base URL must start with **`https://export.arxiv.org`**. Add this exact parameter string to the end: [[1](https://cundy.me/post/elfeed/)]

- `/api/query?search_query=cat:cs.LG+OR+cat:eess.SP&sortBy=submittedDate&sortOrder=descending&max_results=50` [[1](https://cundy.me/post/elfeed/)]