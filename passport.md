# Security Passport

_Switch the ⏳ into ✔️ once have completed the relevant exercises_

| Mission | How to win | Done? | Refs |
| ----- | ----- | ----- | ----- |
| **Set the ground rules** | Decide and document your security criteria. Create a custom property on each repo for its risk tier and set clear approval rules. | ⏳ | NIST SSDF PO.4.1–PO.4.2 (define criteria and track software security checks). |
| **Lock down your code** | Enforce least‑privilege: ensure repositories use CODEOWNERS and branch protection. Only the right people can approve changes. | ⏳ | NIST SSDF PS.1.1 (least‑privilege storage and commit signing); CRA essential requirement for authorised access and integrity protection. |
| **Release with integrity** | Use signed, immutable releases and share cryptographic hashes/attestations so consumers can prove a release hasn’t been tampered with. | ⏳ | NIST SSDF PS.2.1 (publish integrity verification info); SLSA Build L1/L2 (consistent build process and provenance); CRA requires secure-by-default updates. |
| **Keep the receipts** | Archive release artifacts and SBOMs in GitHub Releases and share provenance data for each build. | ⏳ | NIST SSDF PS.3.1–PS.3.2 (archive releases and collect/share provenance); SLSA Build L2 (hosted build platform signs provenance); CRA mandates SBOM and vulnerability handling. |
| **Know your ingredients** | Enable the dependency graph and export an SBOM. Use Dependabot alerts and the dependency review action to prevent adding vulnerable dependencies. | ⏳ | NIST SSDF PS.3.2 (collect and share provenance/SBOM); CRA requires an SBOM and due diligence on third‑party components. |
| **Write safe code** | Turn on CodeQL code scanning. Fix high/critical alerts before merging. Use Copilot Autofix to remediate issues. | ⏳ | NIST SSDF PW.5.1 (follow secure coding practices); PW.7.1/PW.8.1 (code review and testing) – determine appropriate analysis/testing. |
| **Stop secret leaks** | Run the secret risk assessment to find existing leaks. Enable secret scanning and push protection to block secrets from entering your repos. | ⏳ | NIST SSDF PS.1.1 (least‑privilege and secure storage); CRA essential requirement for confidentiality protection. |
| **Handle vulns like a pro** | Monitor Dependabot and CodeQL alerts in the Security Overview. Use security campaigns to prioritise and fix issues. | ⏳ | NIST SSDF RV.1.1 (gather vulnerability information) and RV.2.1 (analyse each vulnerability to plan remediation); CRA mandates regular testing and vulnerability remediation. |
| **Set the rules** | Create organization‑level rulesets for each risk tier. Require reviews, dependency review, and zero critical issues before merging. | ⏳ | NIST SSDF PW.7.1/PW.8.1 (decide on code reviews and testing) and PS.1.1 (least‑privilege controls); CRA essential requirement for secure‑by‑default configuration. |
| **Trust your builds** | Add artifact attestation to your CI workflows – generate and sign an SBOM during the build and attach it to each release. | ⏳ | NIST SSDF PS.2.1 and PS.3.2 (publish integrity info and provenance); SLSA Build L2/L3 (hosted build platform with signed provenance); CRA requires SBOM and secure update distribution. |
| **Track and report** | Use the Security Overview to generate monthly reports. Share progress and metrics with stakeholders and celebrate your achievements\! | ⏳ | NIST SSDF RV.2.1 (analyse vulnerabilities to plan remediation); CRA recommends ongoing monitoring and reporting of vulnerability handling. |
|  |  |  |  |



