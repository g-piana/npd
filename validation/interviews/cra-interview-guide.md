# CRA Silent Watchdog — Mom Test Interview Guide

> Purpose: Validate whether SME manufacturers of connected products have
> a real, painful, paying-to-solve problem with CRA vulnerability
> reporting. Do NOT describe or pitch the product.

---

## Before the interview

- **Who to talk to:** CTO, Head of Engineering, Product Security lead,
  or firmware/embedded team lead at companies that manufacture connected
  products (IoT devices, industrial controllers, edge gateways, sensors,
  robotics components, smart building equipment).
- **Company size sweet spot:** 20-500 employees, €2M-€100M revenue.
  Large enough to be in CRA scope, small enough to lack dedicated
  security teams.
- **Duration:** 20-30 minutes. Tell them 20. Mean it.
- **Your frame:** You are an engineer researching how manufacturers are
  preparing for CRA. You are NOT selling anything. You are genuinely
  curious. Offer to share anonymized findings with them afterward (this
  is real value — they want to know what their peers are doing).

---

## Opening (2 minutes)

"Thanks for making the time. I'm an engineer who's been digging into
how manufacturers of connected products are handling the new CRA
obligations — particularly the September vulnerability reporting
deadline. I'm not selling anything. I'm genuinely trying to understand
how people in your position are approaching this. Everything you tell
me stays anonymized. And I'm happy to share what I'm hearing from
others afterward."

---

## Questions — in order, with what each reveals

### Block 1: Current reality (the past, not hypotheticals)

**Q1: "Can you walk me through the last time a CVE was reported against
a component in one of your products? How did you find out about it?"**

*What this reveals:* Do they have any monitoring process at all? Is it
manual or automated? How do they become "aware" (the CRA trigger word)?
If the answer is "that hasn't happened" or "I'm not sure" — that's a
very strong signal that they have no process.

**Q2: "If I told you there was a critical vulnerability in libcurl 8.4.0
right now, how long would it take you to determine if any of your
products are affected?"**

*What this reveals:* Do they have SBOMs? Can they trace component
dependencies? This is a concrete, measurable question — the answer is
either "minutes" (they have tooling), "hours to days" (manual process),
or "I honestly don't know" (no process at all). The time-to-determine
is the core pain metric.

**Q3: "What does your current bill of materials look like for your
firmware/software? Where does it live?"**

*What this reveals:* SBOM maturity. Likely answers: "we have a
requirements.txt / package.json" (partial, not CRA-compliant), "our
build system produces something" (possibly machine-readable but not
managed), "we have component lists in a spreadsheet" (manual), or
"we don't really have one" (the majority case for SMEs).

### Block 2: CRA awareness and preparation

**Q4: "How much attention have you been paying to the CRA September
deadline? What's your plan?"**

*What this reveals:* Awareness level and whether they've taken any
action. Listen for: denial ("it won't apply to us"), vague intent
("we're looking into it"), active preparation ("we've hired a
consultant / bought a tool"), or anxiety without action ("we know we
should do something but...").

**Q5: "If a critical vulnerability hit at 2am on a Friday and the
24-hour clock started, what would happen in your organization? Who
would handle it?"**

*What this reveals:* Incident response readiness. Do they have a
designated reporting authority? An escalation path? Or would it be
"someone would probably email the CTO"? This question makes the
abstract regulation concrete and personal — watch their reaction.

### Block 3: Existing tools and workarounds

**Q6: "What tools are you using today for anything security-related —
vulnerability scanning, dependency tracking, anything?"**

*What this reveals:* Current tooling landscape. Are they using
Snyk/Dependabot for web components but nothing for firmware? Are they
using any SBOM tools? Or is the honest answer "nothing"? This tells
you what the actual competitive alternative is — likely "nothing" or
"ad hoc manual process."

**Q7: "Have you looked at any CRA compliance tools or services? What
happened?"**

*What this reveals:* Whether they've actively searched and rejected
options (important to understand why), or whether they haven't even
looked. If they looked: what did they find? What was wrong with it?
(Too expensive? Too complex? Didn't fit embedded/firmware context?)

### Block 4: Willingness to pay (approach carefully)

**Q8: "What would need to be true for you to spend money on solving
this before your first actual incident?"**

*What this reveals:* The buying trigger. Is it a fine making headlines?
A customer audit? A board question? An insurance requirement? This
question doesn't ask "would you buy my thing" (Mom Test violation) —
it asks what external event would trigger purchasing behavior. If they
can't answer, the buying trigger doesn't exist yet.

**Q9: "What's your budget process like for compliance or security
tools? Who would need to approve it?"**

*What this reveals:* The purchase path. At an SME, is it the CTO's
credit card? A procurement process? Board approval? This determines
your sales cycle length and pricing ceiling.

---

## Closing (2 minutes)

"This has been really helpful. Two last things: First, based on what
you've told me, is there anything I could share with you that would
be useful? [Offer to send the Article 14 guide or anonymized findings.]
Second, is there anyone else you know — another manufacturer dealing
with the same CRA questions — who might be worth talking to?"

---

## After the interview: what to record

For each interview, capture in `validation/interviews/`:

1. **Company profile** (anonymized): product type, approximate size,
   sector (industrial / consumer / edge / robotics / other)
2. **SBOM maturity** (0-3): 0=none, 1=partial (requirements file),
   2=machine-readable but not managed, 3=automated SBOM pipeline
3. **Time-to-determine** (for Q2): minutes / hours / days / "don't know"
4. **CRA awareness** (0-3): 0=unaware, 1=aware but no action, 2=planning,
   3=actively preparing
5. **Incident response readiness** (0-3): 0=no process, 1=ad hoc,
   2=documented but untested, 3=tested and drilled
6. **Current tools:** list what they use
7. **Buying trigger:** what would make them spend money
8. **Key quotes:** 2-3 verbatim quotes that capture their situation
   (anonymized)
9. **Referrals:** who else they suggested talking to

---

## Kill criteria (decision after 5 interviews)

**Kill the idea if:**
- 4+ out of 5 interviewees say they'll handle CRA reporting with
  existing tools (Jira, email, Excel) and don't see the 24-hour
  scenario as a real problem
- 4+ out of 5 have never had a CVE affect their products and don't
  believe it will happen ("we're too small to be targeted")
- The dominant response to Q8 is "nothing — we'll deal with it when
  we get fined" and there's no intermediate buying trigger

**Strong proceed signal if:**
- 3+ out of 5 cannot determine component impact in less than a day
  (Q2 time-to-determine is days or "don't know")
- 3+ out of 5 have no designated incident response authority (Q5)
- At least 2 express active anxiety about September and are looking
  for solutions (Q4/Q7)

---

## Reminders from The Mom Test

- **Talk about their life, not your idea.** No product descriptions.
  No "what if there was a tool that..." No feature wishlists.
- **Ask about specifics in the past, not hypotheticals about the
  future.** "Tell me about the last time..." not "Would you...?"
- **Talk less, listen more.** If you're talking more than 30% of the
  time, you're pitching, not learning.
- **Compliments are noise.** If they say "that sounds really useful"
  — that's a compliment, not a commitment. Ignore it. The only
  signals that matter are: how they behave today, what they've
  tried, what they've spent money on.
- **The meeting succeeded if you learned something that could change
  your plan.** If every interview confirms what you already believed,
  you're either asking leading questions or not talking to the right
  people.
