
SIEM and EDR give you visibility. This room covers what ties them together operationally, Security Orchestration, Automation, and Response, and why a SOC drowning in disconnected tools and alert fatigue eventually needs something like it.

# Task 1: Introduction
No question, framing only: traditional SOC challenges, then how SOAR addresses them, then a practical playbook walkthrough.

# Task 2: Traditional SOC and Challenges

A SOC's core capabilities: **Monitoring and Detection** (mainly via SIEM), **Recovery and Remediation** (isolating/shutting down infected endpoints via EDR, firewalls), **Threat Intelligence** (continuous feeds of bad IPs, hashes, domains), and **Communication** (coordinating with IT and management).

The challenges that come with running all of this manually: **Alert Fatigue** (numerous tools trigger a flood of alerts, most of them noise), **Too Many Disconnected Tools** (analysts manually pivot between SIEM, firewall, logs with no integration), **Manual Processes** (undocumented incidents slow every future investigation), and **Talent Shortage** (overwhelmed analysts become less effective, which makes hiring harder too, a feedback loop).

**How would you describe the experience of an overload of security events being triggered within a SOC?**
<details><summary>Answer</summary>Alert Fatigue</details>

# Task 3: Overcoming SOC Challenges with SOAR
SOAR's pitch is unifying every tool an analyst touches into one interface, plus a built-in ticketing system for structured incident tracking.
![](4b6a9055cb1dfa1d4947506d169dda15.png)

**Orchestration** solves the disconnected-tools problem specifically, it defines workflows (**playbooks**) that pull from multiple tools without the analyst manually switching between them. Compare the two flows for a VPN brute-force alert:

_Without SOAR:_ check SIEM for the user's normal login IP → check threat intel for IP reputation → check IAM to disable the user if login succeeded → log it in a separate ticketing system.

_With SOAR:_ one workflow does all of it, receive alert → query SIEM → check TI → query SIEM again for successful login → escalate to containment, all inside one interface.

**Automation** means the playbook runs without manual clicks at each step, at scale, closing hundreds of routine alerts without an analyst touching most of them individually.

**Response** can also be automated directly, isolating an endpoint or blocking an IP on the firewall without waiting on a human to execute each action.

Critically, none of this replaces the analyst. Playbooks are built by analysts, and the decisions at genuinely ambiguous points still require human judgment, SOAR removes the repetitive plumbing, not the thinking.

**The act of connecting and integrating security tools and systems into seamless workflows is known as?**
<details>
<summary>Answer</summary>
Orchestration
</details>

**What do we call a predefined list of actions to handle an incident?**
<details>
<summary>Answer</summary>
Playbook
</details>

# Task 4: Building SOAR Playbooks

**Phishing Playbook.** Suspected email arrives → create a ticket → check contents: no URL/attachment → notify the user; URL present → check against VirusTotal, block if malicious; attachment present → hash-check, block if malicious; both present and clean → sandbox them → quarantine, block sender, notify user. The automation payoff here is specifically the URL/attachment checks, that's the slow, repetitive part a human would otherwise do manually on every single phishing report.

**CVE Patching Playbook.** SOAR ingests new CVE details → assess risk threshold → check asset inventory for exposure → create a patching ticket → test in staging → push to production if validated. This automates the tracking and triage side of patch management, the parts that create backlog when done manually, while still routing through a human validation gate before production.

**Is manual analysis vital within a SOAR workflow? Yay or Nay?**
<details>
<summary>Answer</summary>
Yay
</details>

**From where is the CVE Patching playbook fetching the new CVEs?**
<details>
<summary>Answer</summary>
Advisory lists
</details>
*(You can refer to the diagram in the room of CVE playbook where in the first step itself it says to monitor it)*

**In the CVE Patching playbook, if the assets are found vulnerable even after the patch is deployed, what does the SOC develop next?**
<details>
<summary>Answer</summary>
Mitigation plan
</details>
*(You can again refer to the diagram again and find it near the end of it where it gives the option whether assets are vulnerable or not)*

# Task 5: Threat Intel Workflow Practical
The hands-on task: for a Threat Intelligence integration workflow, decide which individual actions should be automated versus left manual, across five categories.

**Case Ticket** — create, assign, communicate, and update can be automated; deletion stays manual.
![](f9f1d26a4b74aad27d027a8576192ad7.png)

**Threat Intel** — fetching intel, setting intervals, and handling failed fetches can be automated; deletion stays manual.
![](c8e89d69cb470d509f7e6d44b81b1e86.png)

**Data Extraction** — extracting domains, URLs, and IPs can be automated; anything unrecognized needs manual handling.
![](4fea1a1ffca4e6077f253db93714e092.png)

**Reputation Checks** — pulling results from VirusTotal can be automated; running tests and validating results stays manual.
![](78657cf54607836bdb496257a0b527d6.png)

**Course of Action** — blocking domains/IPs/URLs and updating tickets can be automated; final approval stays manual.
![](0c8b372fc51858dd7f3165a9b2ea2aa1.png)

<details>
<summary>Flag</summary>
THM{AUT0M@T1N6_S3CUR1T¥}
</details>

The pattern across all five categories is identical: automate data movement and routine actions, keep deletion, validation, and approval as manual gates. That's not arbitrary, those three are exactly the actions where an automation mistake is either irreversible (deletion) or has real consequences if wrong (validation, approval).

# Detection / Defense Note

The design principle in Task 5 is worth generalizing beyond this specific room: any automation workflow, SOAR or otherwise, should automate the reversible, high-volume, low-judgment steps and keep a human gate on anything destructive or requiring contextual judgment. A SOAR playbook that auto-deletes tickets or auto-approves containment actions without a human checkpoint isn't more efficient, it's just moved the risk from "alert fatigue" to "automation making an unreviewed destructive decision."

# Lessons Learned

SOAR doesn't replace SIEM, EDR, or threat intel, it sits on top of them and removes the manual pivoting between them, which is a different problem than detection itself. The automate-vs-manual split in Task 5 is the most transferable idea in the room: data gathering and routine response actions are safe to hand to a playbook, but deletion, validation, and approval consistently stay manual because getting those wrong is expensive in a way that getting a data-fetch wrong isn't.