# Security Passport

Treat this as your security passport: complete each mission and check the box to earn your "stamp".

- [ ] 1. **Classify your assets**  
  - **How to win:** Decide and document your security criteria. Create a custom property on each repo for its risk tier and set clear approval rules.  
  - **Refs:** NIST SSDF PO.4.1–PO.4.2 (define criteria and track software security checks).

- [ ] 2. **Lock down your code**  
  - **How to win:** Enforce least‑privilege: ensure repositories use CODEOWNERS and branch protection. Only the right people can approve changes.  
  - **Refs:** NIST SSDF PS.1.1 (least‑privilege storage and commit signing); CRA essential requirement for authorised access and integrity protection.

- [ ] 3. **Release with integrity**  
  - **How to win:** Use signed, immutable releases and share cryptographic hashes/attestations so consumers can prove a release hasn’t been tampered with.  
  - **Refs:** NIST SSDF PS.2.1 (publish integrity verification info); SLSA Build L1/L2 (consistent build process and provenance); CRA requires secure-by-default updates.

- [ ] 4. **Keep the receipts**  
  - **How to win:** Archive release artifacts and SBOMs in GitHub Releases and share provenance data for each build.  
  - **Refs:** NIST SSDF PS.3.1–PS.3.2 (archive releases and collect/share provenance); SLSA Build L2 (hosted build platform signs provenance); CRA mandates SBOM and vulnerability handling.

- [ ] 5. **Know your ingredients**  
  - **How to win:** Enable the dependency graph and export an SBOM. Use Dependabot alerts and the dependency review action to prevent adding vulnerable dependencies.  
  - **Refs:** NIST SSDF PS.3.2 (collect and share provenance/SBOM); CRA requires an SBOM and due diligence on third‑party components.

- [ ] 6. **Write safe code**  
  - **How to win:** Turn on CodeQL code scanning. Fix high/critical alerts before merging. Use Copilot Autofix to remediate issues.  
  - **Refs:** NIST SSDF PW.5.1 (follow secure coding practices); PW.7.1/PW.8.1 (code review and testing) – determine appropriate analysis/testing.

- [ ] 7. **Stop secret leaks**  
  - **How to win:** Run the secret risk assessment to find existing leaks. Enable secret scanning and push protection to block secrets from entering your repos.  
  - **Refs:** NIST SSDF PS.1.1 (least‑privilege and secure storage); CRA essential requirement for confidentiality protection.

- [ ] 8. **Handle vulns like a pro**  
  - **How to win:** Monitor Dependabot and CodeQL alerts in the Security Overview. Use security campaigns to prioritise and fix issues.  
  - **Refs:** NIST SSDF RV.1.1 (gather vulnerability information) and RV.2.1 (analyse each vulnerability to plan remediation); CRA mandates regular testing and vulnerability remediation.

- [ ] 9. **Put up the guardrails**  
  - **How to win:** Create organization‑level rulesets for each risk tier. Require reviews, dependency review, and zero critical issues before merging.  
  - **Refs:** NIST SSDF PW.7.1/PW.8.1 (decide on code reviews and testing) and PS.1.1 (least‑privilege controls); CRA essential requirement for secure‑by‑default configuration.

- [ ] 10. **Trust your builds**  
  - **How to win:** Add artifact attestation to your CI workflows – generate and sign an SBOM during the build and attach it to each release.  
  - **Refs:** NIST SSDF PS.2.1 and PS.3.2 (publish integrity info and provenance); SLSA Build L2/L3 (hosted build platform with signed provenance); CRA requires SBOM and secure update distribution.

- [ ] 11. **Track and report**  
  - **How to win:** Use the Security Overview to generate monthly reports. Share progress and metrics with stakeholders and celebrate your achievements!  
  - **Refs:** NIST SSDF RV.2.1 (analyse vulnerabilities to plan remediation); CRA recommends ongoing monitoring and reporting of vulnerability handling.


