[2_ronin_bridge_writeup_README.md](https://github.com/user-attachments/files/27796581/2_ronin_bridge_writeup_README.md)
# Web3 Security Incident Analysis — Ronin Bridge Hack (2022)

> **Incident:** Ronin Network Bridge Exploit  
> **Date:** March 23, 2022  
> **Loss:** ~$625 million (173,600 ETH + 25.5M USDC)  
> **Attacker:** Lazarus Group (North Korea) — attributed by US Treasury  
> **Type:** Compromised validator private keys — social engineering

---

## Overview

The Ronin Network is an Ethereum sidechain built by Sky Mavis to support the play-to-earn game Axie Infinity. It uses a bridge to allow users to move assets between Ethereum mainnet and the Ronin sidechain. The bridge was secured by a multi-signature scheme requiring 5 of 9 validator nodes to approve transactions.

In March 2022, attackers compromised 5 of the 9 validator private keys, meeting the threshold required to authorise fraudulent withdrawals — draining the bridge in two transactions.

---

## How the Attack Worked

### Step 1 — Initial Access via Social Engineering
A Sky Mavis employee was targeted with a fake job offer. The attacker sent a PDF containing a malicious payload disguised as an employment offer document. When opened, the PDF executed spyware that gave the attacker access to the employee's internal system.

**MITRE ATT&CK Mapping:**
- T1566.001 — Spearphishing Attachment
- T1204.002 — Malicious File execution

---

### Step 2 — Lateral Movement and Key Compromise
Using the initial foothold, the attacker moved laterally within Sky Mavis's internal network, eventually gaining access to 4 of the 9 Ronin validator private keys held by Sky Mavis.

**MITRE ATT&CK Mapping:**
- T1021 — Remote Services
- T1552 — Unsecured Credentials

---

### Step 3 — Axie DAO Key Compromise
A fifth key was held by the Axie DAO — a separate organisation. Sky Mavis had previously been granted temporary signing permissions by the Axie DAO to handle high transaction volumes, but this permission was never revoked after the need passed. The attacker exploited this to use the DAO's signing authority.

**Root Cause:** Overly permissive and un-revoked access — a governance and access control failure.

---

### Step 4 — Fraudulent Withdrawals
With 5 of 9 keys compromised, the attacker signed two fraudulent withdrawal transactions:
- Transaction 1: 173,600 ETH (~$594M)
- Transaction 2: 25.5M USDC (~$31M)

The transactions were valid from the bridge's perspective — they met the 5-of-9 threshold. No on-chain anomaly detection flagged them.

The exploit went undetected for **6 days** until a user reported they could not withdraw funds.

---

## Root Causes

| # | Root Cause | Category |
|---|---|---|
| 1 | Social engineering via spearphishing | Human / Operational Security |
| 2 | Validator keys stored on internet-connected systems | Key Management |
| 3 | Excessive validator concentration in one organisation (Sky Mavis held 4 of 9) | Decentralisation failure |
| 4 | Axie DAO signing permission not revoked after temporary use | Access Control |
| 5 | No real-time monitoring for large or anomalous withdrawals | Detection failure |
| 6 | 6-day detection gap — no automated alerting | Incident Response failure |

---

## What Should Have Prevented It

1. **Hardware Security Modules (HSMs)** — validator private keys should be held in air-gapped HSMs, not on networked machines
2. **Stricter validator distribution** — no single organisation should hold enough keys to reach threshold alone
3. **Access revocation policy** — temporary permissions must be time-limited and automatically expired
4. **Real-time on-chain monitoring** — large withdrawal events should trigger automatic alerts (e.g. withdrawals above a threshold requiring additional human review)
5. **Employee security training** — spearphishing via fake job offers is a well-known Lazarus Group tactic that security awareness training directly mitigates

---

## Timeline

| Date | Event |
|---|---|
| Nov 2021 | Sky Mavis granted temporary Axie DAO signing permission |
| Mar 23, 2022 | Attacker executes two fraudulent withdrawals |
| Mar 29, 2022 | User reports withdrawal failure — exploit discovered |
| Mar 29, 2022 | Ronin bridge halted |
| Apr 2022 | US Treasury attributes attack to Lazarus Group (North Korea) |
| Jun 2022 | Sky Mavis reimburses affected users ($150M fundraise) |

---

## Key Takeaways for Web3 Security

- **Bridges are high-value targets** — they hold pooled assets and often have complex, less-audited signing logic
- **Multi-sig is only as strong as key distribution** — threshold schemes fail if one party controls too many keys
- **Social engineering bypasses on-chain security entirely** — the smartest contract cannot defend against a compromised key
- **Detection speed matters as much as prevention** — a 6-day detection gap turned a bad incident into a historic one
- **Access control hygiene is foundational** — temporary permissions that aren't revoked become permanent attack surface

---

## References

- [Ronin Network Post-Mortem](https://roninchain.com/blog/posts/post-mortem-ronin-network)
- [US Treasury OFAC Designation](https://home.treasury.gov/news/press-releases/jy0768)
- [Chainalysis Ronin Analysis](https://blog.chainalysis.com/reports/axie-infinity-ronin-bridge-hack/)
