
Same idea as the humans room, different attack surface. This one covers how systems get compromised, sometimes without a single person clicking anything, and puts you through four cases where the right fix depends on correctly identifying the failure mode.

# Task 1: Introduction

No question, just framing: understand what a "system" means in this context and how attacks on systems differ from attacks on people.

Prerequisites:
[1- Junior Security Analyst Intro](1-%20Junior%20Security%20Analyst%20Intro.md)
[3- Humans as attack vectors](3-%20Humans%20as%20attack%20vectors.md)

# Task 2: Definitions of System

A system can be a physical server, a virtual machine, or a cloud platform, and a lot of sensitive data (emails, bank details) lives on shared infrastructure like this. That's exactly why a single breached server can compromise thousands of accounts at once, the blast radius isn't limited to one user.

![](attachment/c17f30aafba82867afd7d45f892e0450.png)

**Can cyber attacks happen without victim intervention (Yea/Nay)?**
<details>
<summary>Answer</summary>
Yea
</details>

**Can a breach of just a single system lead to disastrous consequences (Yea/Nay)?**
<details>
<summary>Answer</summary>
Yea
</details>

# Task 3: Attacks on systems

Three distinct ways systems get compromised, and each needs a different response:

1. **Human-led** — malicious USB, malware from a pirated site, weak passwords. The entry point is still a person's decision, even though the target is a system.
2. **Vulnerabilities** — flaws in the software itself. If an attacker finds one before the vendor does, that's a zero-day. Once it's public, it gets a CVE number, and it becomes a race, defenders patching against attackers building exploits off the same disclosure.
3. **Supply chain** — the software you trust pulls in libraries you never personally audited. If one of those gets compromised and pushes a malicious update, everything downstream that trusted it is compromised too, without anyone on your side doing anything wrong.

**What is the term for a security flaw that can be exploited to breach a system?**
<details>
<summary>Answer</summary>
Vulnerability
</details>

**What is the name of the attack when malware comes from a trusted app or library?**
<details>
<summary>Answer</summary>
Supply chain
</details>

# Task 4: Vulnerabilities

Every piece of software has vulnerabilities somewhere, it's a matter of when and who finds them first. A CVE number gets assigned once a vulnerability goes public, and a patch is the actual fix for it.

**What is the CVE for the critical SharePoint vulnerability dubbed "ToolShell"?**
![](attachment/48a2a5bce5b589ce2deefc03bb70685c.png)
<details>
<summary>Answer</summary>
CVE-2025-53770
</details>

**How would you respond to a detected vulnerability on your system?**
<details>
<summary>Answer</summary>
patch
</details>

# Task 5: Misconfigurations

A **misconfiguration** is a setup mistake, not a code flaw, which means a patch can't touch it. The only real fix is reconfiguring the system correctly. Penetration testing is the authorized version of an attack, specifically aimed at surfacing these before a real attacker does.
![](attachment/f1b2f585c0e3c9f35b7bfb2fb3e00e0c.png)

**Can a system patch or software update fix the misconfigurations (Yea/Nay)?**
<details>
<summary>Answer</summary>
Nay (Better setup is only required to fix misconfiguration)
</details>

**Which activity involves an authorized cyber attack to detect the misconfigurations?**
<details>
<summary>Answer</summary>
Penetration testing
</details>

# Task 6: Practice

You can't train the system but train your IT department
![](attachment/8d63de2de97e6e7b57448108a7c206d6.png)

### Systems at Risk

**Case 1 — A system is now exposed.** → Patch and update to close whatever vulnerability caused the exposure.![](attachment/86b5160daf83bd40e5c1013faa4fa62c.png)

**Case 2 — Root cause traces to a weak admin password.** This is a human-led systems attack, not a code-level flaw, so patching wouldn't touch the actual problem. → Change the admin password first, before anything else.
![](attachment/d4b5feea6f5bbd27ca24f76cf24b752c.png)

**Case 3 — Need to confirm no backdoors remain.** → Patch and confirm no known CVEs are present, the priority is ruling out persistent access left behind.
![](attachment/bc094b14b5a9d3faeeda442677330f0d.png)

**Case 4 — Compromise traced to a trusted, already-installed app.** When the compromise comes through something already trusted rather than something newly installed, supply chain is the more likely explanation over a standalone bug in the app itself. → Treat it as a supply chain attack.
![](attachment/f4ffb7e22f2dc0a7295fc9e6e969704b.png)

<details>
<summary>Flag</summary>
THM{patch_or_reconfigure?}
</details>

### Remediation Plan

Four pillars cover the actual ground here: antivirus protection, a secure password policy, ongoing security awareness training, and patch management. Antivirus and password policy handle baseline hygiene, training keeps people from reintroducing risk that tooling can't catch on its own, and patch management is what keeps known CVEs from sitting open long enough to get weaponized.

The reason training and patch management get paired specifically: misconfigurations tend to creep back in through human error even after a system's been hardened once, so this isn't a one-time fix, it's a maintained one.

![](attachment/6ec2ae8f6367c488f787a1594ef9247a.png)

**Q: What flag did you receive after completing the "Remediation Plan" challenge?**
<details>
<summary>Flag</summary>
THM{best_systems_defender!}
</details>

## Detection Note

Case 4 is the one worth flagging for a real SOC pipeline: supply chain compromises are hard to catch with standard vulnerability scanning, because the software itself doesn't get flagged as vulnerable, it's the update mechanism that's compromised. Catching this in practice usually means watching for unexpected outbound behavior after an update lands, not just scanning the binary against known-bad signatures.

## Lessons Learned

The CVE-to-patch pipeline handles vulnerabilities, but it does nothing for misconfigurations or supply chain compromises, which means "we're patched" is not the same claim as "we're secure." Case 2 and Case 4 make the same point from opposite directions: the fix has to match the actual failure mode, credential, setup, or trust relationship, not just default to "patch it" every time.