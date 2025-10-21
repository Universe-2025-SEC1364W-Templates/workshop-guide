# SEC1364W - Secure more, stress less with a risk-based rollout of GitHub Advanced Security

_Facilitated by: [@theztefan](https://github.com/theztefan) and [@CallMeGreg](https://github.com/CallMeGreg)_

## Session Summary

Regulators, customers, and auditors are raising the bar on software security - and organizations need to prove they have the right controls in place, without slowing down innovation.

In this interactive workshop, you'll jump into action to help "Acme Corp" recover from a breach by rolling out GitHub Advanced Security (GHAS) using a risk-based approach. You'll work inside a live GitHub Enterprise Cloud (GHEC) organization to classify repositories by risk, apply the right security configurations, and automate governance with organization-wide rulesets.

You'll also see how this practical playbook helps you meet compliance requirements for frameworks like NIST SSDF, CRA, and SLSA - all while keeping your developers moving fast and focused on what they do best: building great software.

## Key Takeaways

- Learn how to manage a risk-based rollout of GHAS across your organization  
- Discover how to meet compliance requirements without slowing down your developers  
- See how to satisfy frameworks like NIST SSDF, CRA, and SLSA in under two hours

## Scenario

Your team plays the role of the AppSec group at **Acme Corp**. A breach in the *Mona Gallery* SaaS application exposed plain‑text secrets in configuration files and vulnerable dependencies. Attackers phished a developer, cloned the main repository, discovered secrets, exploited outdated libraries and extracted customer data. Leadership wants to tighten security without hurting developer experience or hindering GitHub Copilot adoption. Your mission is to assess risk and implement controls aligned with NIST SSDF, SLSA and the upcoming Cyber Resilience Act.

1. **Spot hot spots** – assess how much damage secrets can cause and where leaks are located.
2. **Categorize repositories** – decide which repositories are business‑critical and which are not.
3. **Add custom properties** – encode the categorization in GitHub so it can be used later.
4. **Create security configurations** – build two different sets of security configurations (critical vs. non‑critical) and connect them to your categorization.

## Exercises

This workshop is divided into three sections, each with multiple exercises. You should be able to complete all exercises in about 1.30 hours. If you get stuck, don't worry, just raise your hand and we will come by to help you out.

Let's get started!

- [Section 1 - Repo risk rating](section1-repo-risk-rating.md)
- [Section 2 - Security configurations](section2-security-configurations.md)
- [Section 3 - Always on](section3-always-on.md)

You can track your progress with the [Security Passport](security-passport.md). After each section, return to the Security Passport to mark off your progress. By the end of the workshop, you should have completed all the stamps on your Security Passport!
