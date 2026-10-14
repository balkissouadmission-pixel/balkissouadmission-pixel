# Day 15 — Risk Assessment: Risk Register for Metasploitable2

## Executive Summary

Of the 113 findings from the Day 14 Nessus vulnerability scan, seven were carried into this risk register as the ones requiring prioritized attention. Three are rated **Critical** (a default password on the VNC service, an unauthenticated root-level backdoor, and an unsupported operating system), two are rated **High** (BIND DNS and Samba/SMB), and two are rated **Medium** (NFS shares and weak SSL/TLS configuration). All seven currently have no existing control in place and should be mitigated before the system is used for anything beyond an isolated lab.

## Objective

The objective of Day 15 was to convert the vulnerability findings from Day 14 into a risk register that prioritizes security risks and recommends appropriate treatment actions.

A vulnerability scan identifies technical weaknesses, but a risk assessment goes further by considering the likelihood of exploitation, potential impact, existing controls, and the actions needed to reduce risk. This exercise focused on applying that process in a lab environment, loosely following the risk assessment approach described in NIST SP 800-30 and ISO/IEC 27005: identify the asset and threat, assess likelihood and impact, assign a risk rating, and select a treatment strategy.

## Skills Practiced

- Creating a risk register using assets, threats, vulnerabilities, likelihood, impact, and risk ratings
- Translating technical vulnerability findings into business-oriented risk descriptions
- Applying a qualitative risk rating methodology
- Assigning a risk treatment strategy (Mitigate, Accept, Transfer, or Avoid) to each risk
- Estimating residual risk after a recommended treatment is applied
- Identifying risk owners and documenting target remediation dates
- Using vulnerability assessment results as evidence for risk assessment

## Commands / Tools Practiced

- No CLI commands this session — this exercise built a risk register from Day 14's existing scan results rather than running new tools.
- **Nessus Essentials:** Vulnerability assessment results from Day 14
- **Microsoft Excel:** Risk register and qualitative risk rating scale
- **Source documentation:** `day14-vulnerability-assessment.md`
- **Spreadsheet:** `day15-risk-register.xlsx`

The spreadsheet contains two tabs: `Risk Register` and `Legend & Scale`.

## Process (In My Own Words)

For Day 15, I used the findings from my Day 14 Nessus scan to create a risk register rather than treating the scan results as a list of technical vulnerabilities.

The scan produced 113 findings. Instead of transferring every finding into the register, I selected seven representative risks for prioritization: three Critical findings, two High findings, and two Medium findings.

The selected findings covered the VNC default password, a bind shell on port 1524, the end-of-life Ubuntu operating system, BIND DNS, Samba/SMB, NFS shares, and weak SSL/TLS configurations.

This selection allowed me to focus on findings with different levels of severity, exposure, and potential impact.

### 1. Assessing Likelihood and Impact

For each entry, I assessed likelihood and impact separately before assigning the final qualitative risk rating.

Likelihood represents how plausible exploitation is, considering factors such as service exposure, attack requirements, and the weakness identified. Impact represents the potential consequences for confidentiality, integrity, and availability if exploitation succeeds.

The ratings were based on the lab findings and reasonable assumptions about exposure. Because Metasploitable2 is a deliberately vulnerable lab machine rather than a production business system, these ratings are illustrative and would need to be reassessed in a real organizational environment.

For each risk I also assigned a treatment strategy. In risk management, the standard options are to **Mitigate** (reduce likelihood or impact through a control), **Accept** (take no action because the risk is within tolerance), **Transfer** (shift the risk, for example through insurance or a third party), or **Avoid** (eliminate the risk entirely, usually by removing the asset or service). Every risk in this register was assigned Mitigate, since each has a clear, available fix and none met a reasonable bar for acceptance given the severity involved.

I also estimated a residual risk rating for each item — the rating that would likely remain after the recommended treatment is correctly applied. This is an estimate, not a guarantee, and in a real environment it would need to be confirmed with a follow-up scan rather than assumed.

### 2. Critical Risks

**R-01 — VNC service**

The VNC finding involved a default or weak password (CVSS v2.0 base score 10.0). If the service is reachable and the credentials are accepted, an attacker may gain unauthorized remote desktop access. I rated the likelihood and impact as High, resulting in a Critical risk rating under my qualitative scale.

Recommended treatment: Set a strong, unique password and restrict network access. Disable the service if it is not required.
Treatment strategy: Mitigate.
Estimated residual risk: Low, assuming a strong password is set and access is restricted. This should be re-verified with a follow-up scan.

**R-02 — Bind shell on port 1524**

The bind shell finding indicated a dangerous service providing unauthorized shell access (CVSS v3.0 base score 9.8; CVSS v2.0: 10.0). Nessus confirmed root-level access by executing the `id` command through the shell, which returned `uid=0(root)`. This is verified, documented evidence of an unauthenticated compromise, not a theoretical risk.

I rated likelihood and impact as High, resulting in a Critical rating.

Recommended treatment: Investigate the host for compromise, remove the unauthorized service, and rebuild the system from a trusted image, since the confirmed root access means the system's integrity cannot be trusted as-is.
Treatment strategy: Mitigate.
Estimated residual risk: Low, assuming the system is rebuilt from a trusted image and the service is not reintroduced. This should be re-verified with a follow-up scan.

**R-03 — End-of-life Ubuntu operating system**

The system was running Ubuntu 8.04 (CVSS v3.0 base score 10.0 for the SEoL finding), an operating system that has long been out of standard security support. Unsupported software may contain known vulnerabilities that no longer receive normal security fixes.

I rated likelihood and impact as High, resulting in a Critical rating. The concern is not that every vulnerability discovered since the operating system's release is necessarily present, but that continued use increases exposure to unpatched weaknesses and compatibility risks.

Recommended treatment: Migrate to a currently supported operating system. If immediate migration is not possible, isolate the system and restrict access while planning decommissioning or replacement.
Treatment strategy: Mitigate (migration), or Avoid if the asset is decommissioned entirely rather than upgraded.
Estimated residual risk: Low if migrated to a supported release; Medium if only isolated rather than upgraded, since isolation reduces exposure but does not remove the underlying unpatched vulnerabilities.

### 3. High Risks

**R-04 — BIND DNS service**

The BIND finding concerned a DNS vulnerability involving downgrade or reflected denial-of-service behavior (CVSS 8.6). The potential consequences depend on the specific vulnerability, configuration, network exposure, and attack conditions.

I assigned Medium likelihood and High impact, resulting in a High rating under the defined qualitative scale.

Recommended treatment: Verify the installed BIND version and the applicable vendor advisory, then upgrade to a supported version that addresses the identified issue. Restrict unnecessary DNS exposure and apply appropriate network protections.
Treatment strategy: Mitigate.
Estimated residual risk: Low, assuming the upgrade is applied and verified.

**R-05 — Samba/SMB service**

The Samba finding concerned the Badlock vulnerability (CVSS 7.5). The practical exploitability and consequences depend on the affected version, configuration, and attack scenario.

I assigned Medium likelihood and High impact, resulting in a High rating. This is a provisional qualitative assessment rather than a conclusion that exploitation necessarily requires an on-path position.

Recommended treatment: Confirm the affected Samba version and applicable security advisory, upgrade to a supported and remediated version, and restrict SMB access to authorized systems and networks.
Treatment strategy: Mitigate.
Estimated residual risk: Low, assuming the upgrade is applied and verified.

### 4. Medium Risks

**R-06 — NFS shares**

Exposed or overly permissive NFS shares can allow unauthorized users to access files or information (CVSS 7.5). The level of risk depends on network reachability, export configuration, permissions, and access restrictions.

I assigned a Medium rating based on the assumptions used in this lab.

Recommended treatment: Review exported directories, restrict access to authorized hosts, apply least-privilege permissions, and disable unnecessary exports.
Treatment strategy: Mitigate.
Estimated residual risk: Low, assuming exports are restricted and reviewed.

**R-07 — SSL/TLS services**

Weak SSL/TLS protocols or cipher configurations may reduce the protection provided to data in transit (Medium-severity findings across several services). The practical risk depends on the affected protocol, supported cipher suites, service configuration, and an attacker's ability to observe or interfere with communications.

I assigned a Medium rating under the qualitative method used for this exercise.

Recommended treatment: Disable obsolete protocols and weak cipher suites, enable supported secure configurations, and verify the changes through a follow-up assessment.
Treatment strategy: Mitigate.
Estimated residual risk: Low, assuming obsolete protocols and weak ciphers are disabled and verified.

## Risk Register Summary

| Risk ID | Asset | Risk Rating | Treatment Strategy | Residual Risk (est.) | Recommended Treatment |
|---|---|---|---|---|---|
| R-01 | VNC service | Critical | Mitigate | Low | Set strong credentials and restrict access; disable if unused |
| R-02 | Bind shell, port 1524 | Critical | Mitigate | Low | Investigate potential compromise and rebuild if necessary |
| R-03 | Ubuntu 8.04 operating system | Critical | Mitigate / Avoid | Low–Medium | Upgrade, isolate temporarily, or decommission |
| R-04 | BIND DNS service | High | Mitigate | Low | Verify the finding and upgrade to a remediated version |
| R-05 | Samba/SMB service | High | Mitigate | Low | Verify the finding and upgrade to a remediated version |
| R-06 | NFS shares | Medium | Mitigate | Low | Restrict exports and apply least privilege |
| R-07 | SSL/TLS services | Medium | Mitigate | Low | Disable obsolete protocols and weak cipher suites |

Residual risk values are estimates based on the assumption that the recommended treatment is correctly applied; they are not a substitute for a follow-up scan to confirm remediation.

The full spreadsheet contains the threat descriptions, vulnerability details, likelihood and impact ratings, existing controls, treatment recommendations, treatment strategy, estimated residual risk, risk owners, target dates, and status.

## Risk Rating Method

I used a qualitative scale based on the combination of likelihood and impact, loosely modeled on the risk assessment approach in NIST SP 800-30 and ISO/IEC 27005, simplified for a lab exercise rather than implemented as a full organizational methodology.

| Likelihood | Impact | Resulting Risk Rating |
|---|---|---|
| High | High | Critical |
| High | Medium | High |
| Medium | High | High |
| Medium | Medium | Medium |
| Low | High | Medium |
| Medium | Low | Medium |
| Low | Medium | Low |
| Low | Low | Low |

This is the rating method defined for this lab, not a universal risk-rating standard. Different organizations may use different matrices, scoring rules, and acceptance thresholds.

## Risk Ownership and Remediation Planning

Risk owners and target dates were recorded as illustrative placeholders because Metasploitable2 does not represent a real organization with assigned responsibilities.

In a production environment, a risk owner would be accountable for ensuring that the risk is assessed, tracked, and treated or formally accepted by the appropriate authority. Target dates would depend on organizational policies, remediation service-level agreements, business impact, and change management requirements.

The proposed treatments are recommendations, not evidence that remediation has already occurred. Each risk should remain open until the relevant action is completed, verified, and reassessed.

## Key Takeaways

- A vulnerability assessment identifies technical weaknesses, while a risk register helps prioritize them and determine appropriate responses.
- CVSS severity and organizational risk are related but not interchangeable. Exposure, exploitability, impact, and existing controls all matter.
- A risk register does not need to reproduce every vulnerability finding. Selecting significant risks can make prioritization and communication more useful.
- Every risk needs a named treatment strategy, not just a recommended fix. Labeling each risk Mitigate, Accept, Transfer, or Avoid makes the decision explicit instead of implied.
- A risk rating before treatment and an estimated residual rating after treatment are two different things, and a register that only shows one is incomplete.
- Risk ratings are based on available evidence and assumptions. They should be reviewed when new evidence, environmental changes, or remediation results become available.
- Effective remediation requires verification. A risk rating should not automatically be lowered simply because a corrective action has been reported as complete.
- Unsupported operating systems often require migration, isolation, or decommissioning rather than an ordinary patch.
- Risk ownership and remediation deadlines support accountability and help ensure that findings lead to action.

## Challenges Faced

The main challenge in this exercise was assessing likelihood and impact without a real business context.

Nessus provided technical vulnerability findings, but I still had to determine how the weaknesses could affect the system and how urgently they should be addressed. This required me to consider exposure, possible consequences, attack conditions, and existing controls rather than relying only on vulnerability severity scores.

Another challenge was deciding which findings belonged in the risk register. Day 14 produced 113 findings, so I selected seven representative risks to keep the register focused on prioritization and treatment decisions.

A related challenge was assigning a treatment strategy and estimating residual risk for each item. It is easy to recommend a fix, but naming which treatment category it falls under, and predicting what risk would realistically remain afterward, required more careful judgment than simply restating the vulnerability.

I also had to account for the limitations of the lab environment. Risk owners and target dates could only be illustrative because there was no actual organization, business owner, or remediation policy behind the machine.

This exercise helped me understand that risk assessment involves documented judgment and clear assumptions, not simply copying technical scan results into a spreadsheet.

## Connection to Security Operations and GRC

Risk registers are used in Governance, Risk, and Compliance (GRC), vulnerability management, and information security programs to communicate security risks, assign accountability, and track treatment decisions.

This exercise connected my Day 14 vulnerability assessment with a risk-based decision process. I took technical findings and organized them into prioritized risks with recommended actions, a named treatment strategy, estimated residual risk, illustrative ownership, and target dates.

In a real organization, this information would support discussions among security teams, system owners, IT management, and auditors. The ratings and deadlines would need to follow the organization's risk methodology and remediation requirements.

The project gave me practical experience documenting security risks and translating technical findings into a format that supports decision-making.

## Screenshots

1. **Risk Register tab** — the seven selected risks, including assets, threats, vulnerabilities, likelihood, impact, risk ratings, existing controls, treatment recommendations, treatment strategy, estimated residual risk, risk owners, target dates, and status
2. **Legend & Scale tab** — the qualitative likelihood-and-impact matrix and the definitions used to assign risk ratings
