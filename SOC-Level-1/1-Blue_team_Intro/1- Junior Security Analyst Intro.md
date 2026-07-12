
This is the first room in the SOC Level 1 path. It sets up what a Junior Security Analyst (SOC L1) actually does day to day, then hands you one small realistic scenario to work through. Nothing here is technical yet, it's building the mental model you'll keep using for the rest of the path.

# Task 1: Junior Security Analyst Journey

A SOC L1 analyst is the first line of defense for the company. Alerts land here first, and the job is deciding what's noise and what needs to go up the chain. The day-to-day covers monitoring and investigating alerts, joining SOC workshops, working with other teams, and staying current on new attack and defense techniques.

**Which team do you work with as a Junior Security Analyst?**

<details>
<summary>Answer</summary>

SOC

</details>

# Task 2: Security Operations Center (SOC)
You don't triage alone. The team around an L1 analyst usually looks like this:

- **Senior Analyst** — picks up whatever's too complex for your first pass
- **SOC Engineer** — owns the tooling, configures the alert rules
- **SOC Manager** — keeps the team on track, reports results upward
- **Incident Responder** — only gets pulled in for major incidents

This task is informational, no question attached. Worth internalizing anyway: knowing who owns what is exactly what makes Task 3's escalation decision solvable.

# Task 3: A day in the life of a Security Analyst
This is the one part of the room with actual analytical work. You're dropped into a SIEM dashboard with a live alert to triage.

![](8f44c6e7a35a0858ef22d36b0c2a673c.png)

**What the alert showed:** an IP address sitting in the critical field, with unauthorized login attempts followed about four minutes later by a successful login.

That timing is the whole signal. A single failed login is background noise, SOCs see thousands of those a day. A failed login followed shortly after by a _successful_ one from the same source is a different story: it looks like a brute-force or credential-stuffing attempt that landed.

**What was the malicious IP address in the alerts?**

<details>
<summary>Answer</summary>

221.181.185.159

</details>

**Enrichment step:** before escalating anything, run the IP through a reputation lookup tool. Don't skip this, "an IP tried to log in" and "an IP with a history of attacks tried to log in and succeeded" are very different urgency levels, and you won't know which one you're dealing with until you check.

![](9ac7976cfe22faa97e8f08b16b7d22bf.png)

The room then gives four options for who to escalate this to: SOC Team Lead, Python developer, Sales executive, Security architect. Only one of these is actually in the incident response chain, the developer and sales options have no connection to a live security incident, and the architect designs and maintains systems rather than responding to active ones.

![](3230dc9e07a5818cd97e7cb6b82c67c1.png) 

**To whom did you escalate the alert with the malicious IP?** 

<details>
<summary>Answer</summary>

Will Griffin

</details>

**Containment:** once escalated, the response is to add the IP to the firewall block list, with a comment describing why. That comment habit matters beyond this room, whoever audits the incident later needs a trail of what was done and why, without reconstructing it from memory.

![](2864f424ab68a0cdf24662ddfd777384.png)

**What message did you get after blocking the IP address on the firewall?**

<details>
<summary>Answer</summary>

THM{until-we-meet-again}

</details>

# Detection Note

The real lesson in Task 3 is that login telemetry needs to catch _sequences_, not just individual events. A rule that only fires on failed logins would miss this entirely, since the actual signal is failed-then-succeeded within a tight window. Alert logic built around correlation, not single events, is what catches this class of attack before someone's already inside.
