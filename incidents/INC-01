**Date:** 2026-06-29 | **Severity:** Medium | **Status:** Closed — Contained (no compromise) **Analyst:** Joshua | **Technique:** T1110.003 (Brute Force: Password Spraying)

---

## 1. Executive Summary

Wazuh surfaced a cluster of failed-logon events originating from a single internal host against a Windows 10 endpoint (`DESKTOP-UL3FHI7`). Investigation determined the activity was a **password spray** a single password attempted across multiple user accounts to stay below account-lockout thresholds. All authentication attempts failed; no account was compromised and no follow-on activity was observed. The case was closed as a contained, unsuccessful credential-access attempt, with detection and hardening recommendations recorded below.

---

## 2. Detection

The investigation began with failed-logon alerts in the Wazuh dashboard (Threat Hunting), not with prior knowledge of any attack. The initial observation was a tight burst of events sharing the same rule and target host:

![[Screenshot 2026-06-29 211236 2.png]]

| Field            | Observed value                                         |
| ---------------- | ------------------------------------------------------ |
| Rule             | 60122 - "Logon Failure - Unknown user or bad password" |
| Windows Event ID | 4625 (failed logon)                                    |
| Target host      | DESKTOP-UL3FHI7                                        |
| Volume           | Multiple failures within a few seconds                 |

A single failed logon is routine noise. What drew attention here was the **clustering** several failures against one host in a very short window which warranted triage.

---

## 3. Triage

The key triage question: _is this a user mistyping a password, or something coordinated?_ To distinguish, a single 4625 event was expanded to examine its fields:

Placeholder

The fields immediately reframed the activity as suspicious rather than benign:

|Field|Value|Significance|
|---|---|---|
|`data.win.system.eventID`|4625|Confirmed failed logon|
|`data.win.eventdata.ipAddress`|192.168.1.29|**Single source** for all attempts|
|`data.win.eventdata.targetUserName`|varied (user, administrator, admin, guest)|**Multiple accounts** targeted|
|`data.win.eventdata.logonType`|3|Network logon (remote, not console)|

A normal mistyped password is _one user, one source, repeated_. Here the pattern was _one source, many different accounts, one attempt each, seconds apart over the network_ — the defining signature of a password spray.

---

## 4. Investigation & Findings

Correlating the burst confirmed the assessment:

- **Single origin:** every failed logon traced to `192.168.1.29`, indicating one actor, not distributed user error.
- **Account fan-out:** the attempts walked across multiple usernames rather than hammering one consistent with spraying to avoid per-account lockout.
- **Network vector:** logon type 3 indicated remote authentication (SMB), not someone physically at the keyboard.
- **Timing:** all events fell within a few seconds automated, not manual.

**Conclusion:** the activity is a password spray, mapped to **MITRE ATT&CK T1110.003** (Brute Force: Password Spraying), conducted from internal host `192.168.1.29` against `DESKTOP-UL3FHI7` over SMB.

---

## 5. Impact Assessment

| Question                                            | Finding                                                        |
| --------------------------------------------------- | -------------------------------------------------------------- |
| Any successful logon?                               | No, all events were 4625 (failure); no 4624 (success) followed |
| Account compromised?                                | No                                                             |
| Follow-on activity (lateral movement, persistence)? | None observed                                                  |
| Data accessed?                                      | None                                                           |

The attempt was **unsuccessful and contained**. The absence of any Event ID 4624 (successful logon) from the source immediately after the burst is the key evidence that no credentials were guessed correctly.

---

## 6. MITRE ATT&CK Mapping

|Tactic|Technique|ID|
|---|---|---|
|Credential Access|Brute Force|T1110|
|Credential Access|Brute Force: Password Spraying|T1110.003|

---

## 7. Response & Recommendations

Detection worked, but the investigation surfaced gaps worth closing:

- **Correlation rule:** the built-in rule (60122) flagged each _individual_ failure but did not raise a single consolidated "password spray" alert. A custom correlation rule should fire when N failures from one source hit multiple accounts within a short window, so an analyst sees one high-signal alert instead of a scattering of low-level ones. (Tracked in detection backlog: `0100-password-spray.xml`, rules 100100/100110/100120.)
- **Account lockout policy:** enforce lockout thresholds so spraying is throttled at the OS level, not only detected after the fact.
- **Source containment:** in production, isolate/block the offending source host and investigate why an internal host is spraying credentials.
- **Monitor for escalation:** alert specifically on a 4624 _success_ from a source that recently generated a 4625 burst that sequence indicates a spray that worked.

---

## Appendix A — Lab Reproduction Notes

> This case was generated in a controlled home lab. The following details are how the activity was produced and are **not** part of the analyst investigation above — they exist so the scenario can be reproduced.

**Environment**

|Role|Host|VM|IP|
|---|---|---|---|
|Attacker|Laptop|Kali Linux|192.168.1.29 (bridged)|
|Victim|Mini PC|Windows 10 Enterprise LTSC|192.168.1.106 (bridged)|
|SIEM|Laptop|Wazuh 4.14.5 manager|192.168.1.193 (bridged)|

**Prerequisites (lab-only)**

- Kali required a bridged adapter to reach the victim across physical hosts (host-only/NAT could not cross from laptop to mini PC).
    
- Victim SMB (445) was deliberately exposed for the test, initial `nmap -p 445` showed `filtered` (Windows Firewall dropping inbound). Enabled with:
    
    ```powershell
    Enable-NetFirewallRule -DisplayGroup "File and Printer Sharing"
    ```
    
    Re-scan confirmed `445/tcp open`.
    
    Placeholder    

**Attack command (from Kali)**

`users.txt` contained: `user`, `administrator`, `admin`, `guest`.

```bash
netexec smb 192.168.1.106 -u users.txt -p 'Password123'
```

Output — all four attempts rejected by design (failures are the objective; they generate the 4625 events the detection relies on):

Placeholder

```
SMB  192.168.1.106  445  DESKTOP-UL3FHI7  [-] DESKTOP-UL3FHI7\user:Password123          STATUS_LOGON_FAILURE
SMB  192.168.1.106  445  DESKTOP-UL3FHI7  [-] DESKTOP-UL3FHI7\administrator:Password123 STATUS_LOGON_FAILURE
SMB  192.168.1.106  445  DESKTOP-UL3FHI7  [-] DESKTOP-UL3FHI7\admin:Password123         STATUS_LOGON_FAILURE
SMB  192.168.1.106  445  DESKTOP-UL3FHI7  [-] DESKTOP-UL3FHI7\guest:Password123         STATUS_LOGON_FAILURE
```
