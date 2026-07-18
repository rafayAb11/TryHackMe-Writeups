
This is the first hands-on room in SOC Team Internals. It moves past theory and puts you in front of an actual simulated SOC dashboard, five live alerts, and asks you to triage them the way an L1 would on shift.

# Task 1: Introduction

No question here, just setup. Worth noting the room links to the [SOC Simulator](https://tryhackme.com/soc-sim) and the [SAL1 certification](https://tryhackme.com/certification/security-analyst-level-1/details), so this room is explicitly building toward both.

# Task 2: Events and Alerts
The chain runs event → log → SIEM/EDR → alert. Something happens on a system (an event), the system records it (a log), that log gets shipped to a security platform, and if it matches a detection rule, the platform raises an alert. Not every log becomes an alert, and not every alert is a real threat, that gap is exactly what triage exists to close.

Alert Management System:
![](attachment/a8065356271e73260ea7d95bf7fdf84e.png)

The people around this pipeline each own a different slice of it: L1 reviews alerts and sorts real from false positive, L2 does the deeper analysis on whatever L1 forwards, SOC Engineers make sure alerts actually carry enough context to triage in the first place, and the SOC Manager tracks triage speed and quality so nothing real slips through.

**What is the number of alerts you see in the [SOC dashboard (opens in new tab)](https://static-labs.tryhackme.cloud/apps/socl1-alerttriage/)?**
![](attachment/e7aea4000d293e7211a7c581b1a4f3d9.png)
<details>
<summary>Answer</summary>
5 (2 are closed)
</details>

**What is the name of the most recent alert you see?**
<details>
<summary>Answer</summary>
Double-Extension File Creation
</details>

# Task 3: Alert Properties

Every alert carries the same core fields, and knowing what each one actually tells you is what separates fast triage from guessing:
![](attachment/d094f8e936960f60b93f616bb8a6e986.png)

1. **Alert time** → Creation time
2. **Alert name** → Summary of what happened based on the detection rule
3. **Alert severity** → Defines the urgency of the alert
4. **Alert status** → Indicates whether someone is already working on it
5. **Alert verdict** → Shows the analyst's conclusion
6. **Alert assignee** → Identifies who owns the alert
7. **Description** → Explains what the alert is about
8. **Fields** → SOC analyst's comments

![](attachment/0f4a41ba9c45471b18524f9a826f4322.png)

**What was the verdict for the "Unusual VPN Login Location" alert?**
<details>
<summary>Answer</summary>
False Positive
</details>

**What user was mentioned in the "Unusual VPN Login Location" alert?**
![](attachment/c3efb06962089c74844b0f8e5b4c2fc0.png)
<details>
<summary>Answer</summary>
M.Clark
</details>

# Task 4: Alert Prioritization

Three rules decide what you pick up first, in this order: don't touch anything already assigned, sort by severity (critical, then high, then medium, then low), and within the same severity, take the oldest one first. The severity order exists because detection rules are tuned so critical alerts are the ones most likely to actually be real and damaging, not just arbitrary labeling.

**Should you first prioritize medium over low severity alerts? (Yea/Nay)**
<details><summary>Answer</summary>Yea</details>

**Should you first take the newest alerts and then the older ones? (Yea/Nay)**
<details><summary>Answer</summary>Nay</details>

**Assign yourself to the first-priority alert and change its status to In Progress.**  
**The name of your selected alert will be the answer to the question.**
![](attachment/b23583337a65bfbbe5ca0a2d81b19f61.png)
<details><summary>Answer</summary>Potential Data Exfiltration (the only unassigned critical alert)</details>

# Task 5: Alert Triage
![](attachment/947ffd9d4e66fc3412c5bea2fe7b159b.png)
The actual triage loop, five steps: prioritize and assign to yourself, read the name/description and note the important fields, check if a workbook covers this alert type and follow it if so, otherwise investigate manually in the SIEM, then decide close or escalate to L2, and finally close with a comment explaining the call.

**Which flag did you receive after you correctly triaged the first-priority alert?**
![](attachment/4059a49d315c5455c3aecb8b31870b21.png)
**First-priority alert (critical) — Potential Data Exfiltration.** The large outbound data transfer traced back to `zoom.us`, a legitimate domain. Combined with the fact that this coincided with an online meeting, the large transfer is explained by normal video call bandwidth, not exfiltration. → Verdict: False Positive. Status: Closed, with a comment explaining the Zoom meeting context.
![](attachment/2afd76c154d4e1ff980ecd5cddf4114c.png)
<details><summary>Answer</summary>THM{looks_like_lots_of_zoom_meetings}</details>

**Which flag did you receive after you correctly triaged the second-priority alert?**
![](attachment/b76c1042168b15e9c4c070ef43eb3b31.png)
**Second-priority alert (high) — Double-Extension File Creation.** This is the same alert from Task 2, now confirmed as the second pick because it's High severity, even though it's also the newest, severity beats recency in the prioritization order. The file itself came from a questionable domain and carried a double extension, a classic technique to disguise an executable as something harmless like a PDF or image. → Verdict: True Positive, real malware.
![](attachment/91958027ca71b83082afc530c10b0d5e.png)
<details><summary>Answer</summary>THM{how_could_this_user_fall_for_it?}</details>

**Which flag did you receive after you correctly triaged the third-priority alert?**
![](attachment/b1acacf9a7773d43918fcc2777f9a247.png)
**Third-priority alert (low) — GitHub library download.** A user downloaded a well-reputed, open-source library from GitHub. Nothing here indicates malicious intent, a known-good source and a legitimate package. → Verdict: False Positive.
![](attachment/de4ef83617c93071e48fecec66efb7da.png)
<details><summary>Answer</summary>THM{should_we_allow_github_for_devs?}</details>

## Detection / Defense Note

The pattern across all three cases here is that severity alone never tells the full story, domain reputation and file naming conventions do. `zoom.us` being a real Zoom domain kills the exfiltration read instantly, and a double file extension is a strong enough single indicator to flip a "maybe" into a confirmed True Positive without needing a sandbox detonation first. A detection pipeline that flags double-extension file creation as a standalone high-confidence rule, rather than waiting for a hash match, catches this class of attack earlier.

# Lessons Learned

Severity tells you what order to work alerts in, it doesn't tell you the verdict, that only comes from checking the actual indicators (domain legitimacy, file extension patterns, source reputation). Also worth internalizing: "the newest alert" and "the highest priority alert" are frequently not the same alert, and prioritizing by recency instead of severity is exactly the mistake the room is testing for in Task 4.