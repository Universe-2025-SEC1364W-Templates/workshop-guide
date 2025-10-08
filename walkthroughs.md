# GHAS Roll‑Out Workshop Walkthrough

# Background: From Wild West to Structure

### Scenario

Your team plays the role of the AppSec group at **Acme Corp**.  A breach in the *Mona Gallery* SaaS application exposed plain‑text secrets in configuration files and vulnerable dependencies.  Attackers phished a developer, cloned the main repository, discovered secrets, exploited outdated libraries and extracted customer data.  Leadership wants to tighten security without hurting developer experience or hindering GitHub Copilot adoption.  Your mission is to assess risk and implement controls aligned with NIST SSDF, SLSA and the upcoming Cyber Resilience Act.

Acme Corp's GitHub Enterprise setup currently consists of a single organization with 10 repositories and no centralized security controls.  Each exercise in this workshop builds toward structured governance:

1. **Spot hot spots** – assess how much damage secrets can cause and where leaks are located.  
2. **Categorize repositories** – decide which repositories are business‑critical and which are not.  
3. **Add custom properties** – encode the categorization in GitHub so it can be used later.  
4. **Create security configurations** – build two different sets of security configurations (critical vs. non‑critical) and connect them to your categorization.

## Exercise 1 – Spot the Hot Spots: Secret Risk Assessment

The **secret risk assessment** is a free on‑demand scan available to GitHub Team and Enterprise organizations.  It examines the code in all repositories (including archived ones) and produces a report showing:

- **Total secrets**, **public leaks** and **preventable leaks** – aggregated counts of secrets exposed and how many leaks could be blocked by push protection  
- **Secret locations** – where secrets were found (code in branches, tags and archived repos)  
- **Secret categories** – distribution of partner secrets (e.g., API keys) and generic secrets like SSH keys or JWTs  
- **Repositories with leaks** – which repos contain the leaks

Read more [here](https://docs.github.com/en/enterprise-cloud@latest/code-security/securing-your-organization/understanding-your-organizations-exposure-to-leaked-secrets/about-secret-risk-assessment).

Only organization owners or security managers can run the scan, and it can be generated once every **90 days**. Read more about the secret risk assessment in [our docs](https://docs.github.com/en/enterprise-cloud@latest/code-security/securing-your-organization/understanding-your-organizations-exposure-to-leaked-secrets/viewing-the-secret-risk-assessment-report-for-your-organization).

We have already triggered this scan on your organization before the session.  Each participant receives a link to the finished report. Rather than running the scan yourself, focus on reviewing the results. Observe which Acme Corp. repositories contain leaked secrets and notice that many are the flagship projects (e.g., *mona‑gallery* and *mona*).

To run a similar security risk assessment on your own organization(s) after this workshop, you would need to:

1. Navigate to your organization's **Security** tab (under the organization header)  
2. In the sidebar under **Security**, click **Assessments**  
3. Click **Scan your organization** to start the assessment  
4. GitHub emails the organization owner when the report is ready

### Points to discuss

- The scan is **point‑in‑time** and does not continuously monitor; leaks found in the report could have been prevented if push protection had been enabled.  
- The report counts secrets across public and private repos and provides categories of secrets and impacted repos, helping you prioritize remediation.  
- The free assessment scans code only; GitHub Secret Protection (paid) extends scanning to pull requests, issues, wiki pages and discussions.

## Exercise 2 – Repository Categorization

Before applying security controls, decide which repositories are **business‑critical**, **standard** or **low risk**.  This risk rating directly maps to how strong the security controls should be.  For Acme, the team prepared a document listing each repository and its category.  Review it and think about:

- **Critical (business‑critical)** – contains customer data, production infrastructure or is widely used (e.g., *mona‑gallery*, *mona*, *terragoat‑iac*).  A compromise of these repos has severe impact.  
- **Standard** – internal tools that support the business but aren't directly customer‑facing (e.g., *juice‑shop*, *moshi* library).  
- **Low risk** – early‑stage experiments, documentation or configuration repos.

The following table summarizes each repository in the Acme Corp organization and assigns it to a risk tier (critical, standard, or low). These tiers are based on how important the repository is to the business and how severe the impact would be if it were compromised. Use this classification when creating custom properties and security configurations in later exercises.

| Repository | Risk tier | Reasoning |
| :---- | :---- | :---- |
| **mona‑gallery** | Critical | Main SaaS product; holds customer data and production code; high business impact if compromised. |
| **mono** | Critical | On‑prem variant of mona‑gallery; same functionality and criticality as the SaaS offering. |
| **terragoat‑iac** | Critical | Infrastructure‑as‑code templates deploying production environments; compromise could allow full environment control. |
| **juice‑shop** | Standard | Internal e‑commerce site for company swag; limited sensitive data; should follow secure coding but lower risk. |
| **WebGoat** | Low | Intentionally vulnerable web application for training; used for demonstrations, not part of Acme's services. |
| **moshi** | Standard | Internal JSON parsing library consumed by multiple projects; important to development but not customer‑facing. |
| **OpenAPI.NET** | Low | Fork of the OpenAPI.NET SDK; used for experimentation and library customization, not core to Acme's products. |
| **SecurityShepherd** | Standard | Security training platform used internally; contains practice exercises but not production code. |
| **JavaVulnerableLab** | Low | Intentionally vulnerable Java lab used for security training; not deployed in production. |
| **mission-control** | Standard | Central repository for GitHub settings and workflows; affects multiple repos but contains no direct customer data. |

### How to use this classification

For **Exercise 3** you will create custom properties for each risk tier (e.g., `critical`, `standard`, and `low`) and tag the repositories accordingly. When creating security configurations in **Exercise 4**, apply a stricter configuration to all critical repositories and a more flexible configuration to standard and low‑risk repositories.

## Exercise 3 – Creating Custom Properties

Custom properties add metadata to repositories so you can filter them later when applying rulesets or security configurations.

### Step 1 – Add custom properties for risk categories

1. In the upper‑right corner of GitHub, click your profile picture and choose **Organizations**.  
2. Next to your organization, click **Settings**.  
3. In the sidebar under **Code, planning, and automation -> Repository**, click **Custom properties**.  
4. Click **New property**.  
5. Enter a **Name** (no spaces) and optional description.  For the workshop we recommend creating three boolean properties: `critical`, `standard` and `low`.  Select **True/False (Boolean)** as the type. Leave the other options unchecked.  
6. Click **Save property**.

**Alternative:** Instead of three booleans, you could define a single `risk-category` property with allowed values `critical`, `standard` and `low` (single select or multi select).  This keeps metadata normalized and simplifies reporting.

### Step 2 – Set property values for repositories

1. Return to **Custom properties** in your organization settings.  
2. Select the **Set values** tab.  
3. Choose one or more repositories and click **Edit properties**.  
4. In the dialog, set each property's value according to the categorization document and click **Save changes**.

After this exercise, each repository has metadata indicating whether it's critical, standard or low risk. You can search or filter repositories by these properties using the **Repositories** page (type `prop` in the search bar).

## Exercise 4 – Building Security Configurations

Security configurations are collections of enablement settings for GitHub's security features (secret scanning, dependency scanning, code scanning and more). They let you apply the same settings to many repositories at once and optionally **enforce** those settings so repository admins cannot override them.

This exercise creates two configurations:

- **Critical repositories** – strict settings enabled and enforced.  
- **Non‑critical repositories** – similar features enabled but not enforced (developers can adjust if needed).

### Step 1 – Create the **Critical** security configuration

1. Navigate to your organization settings and choose **Advanced Security -> Configurations**.  
2. Click **New configuration**.  
3. Provide a **Name** like *Critical repos* and a description explaining that it applies to business‑critical repositories.  
4. Under **Secret Scanning**:  
   - Enable **Alerts** (this turns on secret scanning).  
   - Enable **Validity checks** to reduce test if found secrets are still valid and help with prioritization.  
   - Enable **Non‑provider patterns** to detect generic secrets like private keys.  
   - Enable **Scan for generic passwords** to let Copilot detect passwords using AI.  
   - Enable **Push protection** to block commits that contain secrets.  Push protection proactively scans code during the push and prevents secrets from being committed.  
     - Set **Bypass privileges** to `Specific actors` and select Repository admin. This would require an approval from an admin if a bypass of push protection block is required.  
   - Enable **Prevent direct alert dismissals** to have better controls over dimissal of secret scanning alerts given the recent breach. This setting will require actors to submit requests for alert dismissals.  
5. Under **Code Scanning**:  
   - Enable **Default setup** for CodeQL code scanning.  CodeQL identifies vulnerabilities and errors in your code and displays results as alerts.  
   - Keep **Prevent direct alert dismissals** as `Not set` as we do not want to increase unnecessary friction with developers with the introduction of CodeQL.  
6. Under **Dependency Scanning**:  
   - Ensure **Dependency graph**, **Automatic dependency submission**, **Dependabot alerts** and **Dependabot security updates** are enabled.  The dependency graph analyzes the manifest/lock files to list dependencies and highlight vulnerabilities, and Dependabot alerts notify you when you depend on a vulnerable package.  Dependabot security updates can automatically open pull requests to upgrade vulnerable dependencies.  
7. Enable **Private vulnerability reporting** to receive reports from researchers for public OSS repositories.  
8. In the **Policy** section, select **Use as default for newly created repositories** to `None` as we don't want to treat every new repository as **Critical** and set **Enforce configuration** to block repository owners from disabling any enabled features (`Enforce`).  
9. Click **Save configuration**.

### Step 2 – Create the **Non‑critical** security configuration

Repeat the steps above to create a second configuration named *Non‑critical repos*.

Differences:

- Enable **Secret Protection**, **Push protection** and **Code Security** features as above.  
- Leave **Enforce configuration** disabled. This gives teams flexibility to temporarily disable features if needed (consistent with the "no friction" mandate).  
- Leave **Prevent direct alert dismissals** as `Not set` for both Secret Scanning and Code Scanning to avoid increasing friction for developers.  
- Set the configuration as **default for all new repositories** as we want to treat most repos as non‑critical.

### What have we done so far?

By the end of this exercise, you have:

- **Structured the "wild west”** – central governance exists through custom properties and security configurations.  
- **Prepared security configurations** - you have created central security configurations for controlled rollout of the new policies.  
- **Mapped risk categories to security configurations** – critical repos get strict enforcement; others receive recommended settings without enforcement.

# Rolling out guardrails

## Exercise 5 - Apply the security configurations

Now that you've created **Critical** and **Non‑critical** security configurations and tagged each repository with a custom risk property, it's time to attach the configurations to the repositories.

### Step 1 - Apply the configurations using custom properties

1. Navigate to **Settings -> Security** for your organization and open **Advanced Security -> Configurations**.  
2. In the **Apply configurations** table, filter repositories by their custom property.  For example, type `props.critical=true` in the search bar to list only critical repositories.  Select these repositories and choose **Apply configuration -> Critical repos**.  Repeat with `props.standard=true` or `props.low=true` for the *Non‑critical* configuration.  When prompted, review license usage and click **Apply**.  
3. Wait for the configurations to apply.  You can see status indicators next to each repository.

**Tip:** Filtering by custom properties allows you to apply configurations consistently without manually selecting each repository.  GitHub's Rulesets feature, which we'll look at later, allows targeting by property as well.

### Step 2 - Confirm the results on a sample repository

After applying the configurations, open any repository and check that the security features are now active.

1. Go to the repository's main page and click **Security**.  You should see *Dependabot*, *Secret scanning* and *Code scanning* listed under **Vulnerability alerts**.  
2. Click **Dependabot** and verify there are alerts (if any).  You can filter the alerts and click an individual alert to view details.  From the alert page you can create a security update pull request to fix a vulnerable dependency or dismiss the alert with a reason.  
3. Click **Secret scanning**.  Open an alert if present.  To resolve an alert, rotate and revoke the leaked secret, then select **Close as** to record the appropriate reason and optionally add a comment.  
4. Click **Code scanning**.  Open a code scanning alert.  If Copilot Autofix can suggest a fix, click **Generate fix**, then **Create PR with fix**.  Alternatively, manually update the code and open a pull request.  To dismiss an alert you decide not to address, click **Dismiss alert**, choose a reason and optionally add a comment.

### Discussion - Beyond initial scan

At this point you've configured scanning and are addressing vulnerabilities already present in your code.  However, there is still nothing preventing developers from introducing new vulnerable dependencies in future pull requests. The next exercises introduce measures to block insecure dependencies at PR time and to enforce reviews and checks via rulesets.

## Exercise 6 - No new vulnerable dependencies

This exercise introduces the **dependency review action** and shows how to manage it centrally across your organization.  The action scans pull requests for any changes to dependencies and fails the workflow if new dependencies have known vulnerabilities.  You'll implement a central workflow that runs on every pull request and stops vulnerable dependencies from being merged.

### Step 1 - Create or use a central configuration repository

1. Decide on a repository to host your central GitHub Actions workflows (e.g., `mission-control`).  Navigate to this repository and create a new folder called `.github/workflows` if it doesn't already exist.  
2. Create a new file named `dependency-review.yml` inside `.github/workflows` with the following contents:

```
name: "Dependency Review"
on: [pull_request]

permissions:
  contents: read
  pull-requests: write

jobs:
  dependency-review:
    runs-on: ubuntu-latest
    steps:
      - name: "Checkout repository"
        uses: actions/checkout@v5
      - name: "Dependency Review"
        uses: actions/dependency-review-action@v4
        with:
          # Fail if any new dependency with severity >= high is introduced
          fail-on-severity: high
          comment-summary-in-pr: always
          # Optionally allow or deny specific licenses
          # allow-licenses: MIT, BSD-3-Clause
          # deny-licenses: GPL-3.0, LGPL-2.0
```

This workflow uses the `actions/dependency-review-action@v4` to automatically scan dependency changes in pull requests and error if vulnerabilities are found.  You can customize options such as severity thresholds, allowed or denied licenses, and scopes.   
3\. Commit and push the workflow file to the `main` branch of your configuration repository.  
4\. This repository should be public or the workflow we just created be accessible to all other repositories in your organization. You can check this in the repository **Settings -> Actions -> General** section under **Workflow permissions -> Access** make sure to select the `Accessible from repositories in the 'YOUR_ORG_NAME' organization` option.

### Step 2 - (Optional) Use a separate configuration file

The dependency review action supports referencing an external configuration file instead of in‑line options.  For example, you could create `.github/dependency-review-config.yml` in the same repository and set more complex rules there, then set:  
  `config-file: './.github/dependency-review-config.yml'`  
in the workflow.  For the workshop, the inline example above is sufficient.

## Exercise 7 - Rulesets

Rulesets let you enforce rules and required workflows across multiple repositories from a single place.  They can target repositories using names, patterns, or custom properties.  You'll create separate rulesets for **Critical** and **Non‑critical** repositories.

### Step 1 - Create a ruleset for critical repositories

1. Navigate to your organization's **Settings -> Repository -> Rulesets**.  
2. Click **New ruleset -> New branch ruleset**.  
3. Give the ruleset a name such as **Critical Repos Ruleset** and set the enforcement status to **Active**.  
4. In the **Target repositories** section, choose **Repositories matching a filter**.  Add a filter for your custom property: select the property (e.g., `critical`) and set the value to `true`, or use the query syntax `props.critical=true`.  This ensures that only repositories tagged as critical are affected.  
5. Under **Target branches**, select **Include default branch** (you can add other branch patterns if needed).  
6. In the **Rules** section:  
   * Leave the two default checks enabled:  
     1. **Restrict deletions**  
     2. **Block force pushes**  
   * Additionally select:  
     1. **Require a pull request before merging** - enforce that all changes go through pull requests.  
        1. Select **Require review from Code Owners**  
        2. Select **Automatically request Copilot code review**  
     2. **Require code scanning results** - require that no open code scanning alerts of a given severity exist before merging. Add CodeQL as a tool and choose an appropriate severity threshold for critical repositories.  
     3. **Require workflows to pass before merging** - add the `dependency-review` workflow as a required check. Select the **mission-control** repository and select **.github/workflows/dependency-review.yml** from the list.  
     4. In the section when defining required workflows you will want to select **Do not require status checks on creation** to avoid friction and failed workflows when a new empty repository or branch is created.  
7. Click **Create** to finalize the ruleset.

### Step 2 - Create a ruleset for non‑critical repositories

Repeat the steps above but target repositories with the `standard` and `low` properties.  Set the enforcement status to **Evaluate**; this will show in insights which pushes would fail without enforcing them.

In the rules section:

* Include the same **Require pull request before merging** rule.  
* Require the **dependency-review-action** status check so that new vulnerabilities are still blocked.  
* You can make the severity threshold for code scanning results less restrictive or omit it entirely.  
* Consider setting the ruleset to **Evaluate** first; this will show in insights which pushes would fail without enforcing them.

### Step 3 - Verify the ruleset behavior

Once the rulesets are active, create a pull request in a repository targeted by the ruleset:

1. Add a new dependency with a known vulnerability (for example, bump a package to a vulnerable version) and push the branch. You can use [GitHub Advisory Database](https://github.com/advisories) to find a *bad* candidate\!  
   * For example, in **mona-gallery/frontend/package.json**, add the following dependency to the "dependencies” block: **"flowise": "3.0.5"**  
2. The pull request should display a failing **Dependency Review** status check; merging will be blocked until the dependency is removed or updated.  
3. If you attempt to merge while code scanning alerts exist at the configured severity, merging is also blocked due to **code scanning merge protection**.  
4. Review the ruleset insights to see which branches and commits would be blocked or allowed based on the current policies.

### Why use rulesets?

* **Central control** - By defining rules in one place, you ensure consistent governance across multiple repositories.  Dynamic targeting based on custom properties means you don't need to edit the ruleset when new repos are created.  
* **Required workflows** - Rulesets let you add required workflows such as the dependency review action to ensure that all PRs meet your security standards.  
* **Merge protection** - Requiring status checks and code scanning merge protection enforces that vulnerabilities are fixed before code is merged.

With these guardrails in place, Acme Corp has moved beyond reacting to existing issues to proactively preventing new ones.  Developers are alerted early about insecure dependencies, and merges are blocked until policies are satisfied.  This completes the groundwork for further enhancing application security and compliance.

### Step 4 - (Optional) Scan IaC code with a 3rd party code scanning tool

The `terragoat‑iac` repository is how Acme Corp. manages their infrastructure. If you look at the list of [CodeQL supported languages](https://codeql.github.com/docs/codeql-overview/supported-languages-and-frameworks/) you will not find terraform. Given that the repository is categorized as `critical` we would want to introduce a static analysis tool that will scan our terraform files. The extra exercise is to add Trivy as a tool that will scan that repository. With the support of GitHub Copilot can you create a workflow that will **only** run against the `terragoat-iac` repository and it will be required workflow in the general ruleset?

## Exercise 8 - Run a simulation

—

# Always On: Security doesn't sleep

## Exercise 9 - Fix Smarter, Together

Security campaigns are a way to group alerts and prioritize them with developers, so you can focus on remediation of those select vulnerabilities. In this lab we will create a security campaign for identified security alerts in the default branch and see how the Campaigns feature in GHAS will help encourage focussed remediation effort from development teams. With this feature you can fix security alerts at scale by creating security campaigns and collaborating with developers to burn down your security backlog.

For this exercise, we will use the Org level **Security** tab for creating **Campaigns**.

1. In GitHub, navigate to your Organization **Security** tab.  
2. Click on the **Campaigns** menu in the left-hand pane.  
3. Click on the **Create campaign** button.  
4. Select **From template**.  
5. In the subsequent menu, review the existing **Code** campaign templates:  
   1. Critical CodeQL Alerts  
   2. Mitre top 10 KEV  
   3. SQL Injection  
   4. Cross-site Scripting  
6. Select the **Secrets** tab to view the existing **Secrets** campaign templates:  
   1. Active secrets  
   2. Inactive secrets  
   3. Passwords

Challenge: Rather than using a template, can you create a custom campaign for all the open vulnerabilities of severity `critical` and have `autofix` support?

## Exercise 10 - Security Overview

Now that you've enabled CodeQL code scanning, secret scanning with push protection, and Dependabot, it's time to see how your GitHub Organization's Security Overview helps you and your leadership team track security posture.

In this exercise, you'll answer a set of predefined questions from your fictional CISO by gathering data directly from the Security Overview dashboards.

### Step 1 - Open Security Overview

1. Navigate to your organization in GitHub.  
2. In the top navigation, click Security.  
3. You'll land on the Overview dashboard. This view summarizes alerts, trends, and feature adoption across your repositories.

### Step 2 - Answer the CISO's Questions

Use the following dashboards and metrics to find answers.

**Q1: "What percentage of our repositories have CodeQL, Dependabot, and Secret scanning enabled?”**

* Navigate to **Coverage**.  
* Review the adoption cards at the top and note the percentages of repositories with each feature turned on.

**Q2: "How many open alerts do we currently have, and is the number going up or down?”**

* In **Overview** -> **Detection**, check the **Open alerts over time** graph.  
* Change the time range to **Last 90 days**  
* Note the total open alerts, severity breakdown, and whether the trend indicator is rising or falling.

**Q3: "What is the average age of our remaining alerts?”**

* In Detection, find the **Age of alerts** metric.  
* Report the number of days that alerts have been open on average.

**Q4: "Which repositories are contributing the most to our risk right now?”**

* Scroll down to **Impact analysis** -> **Repositories**.  
* Identify the top repositories with the most open alerts.

**Q5: "Are we keeping up with remediating vulnerabilities?”**

* Switch to **Overview** -> **Remediation**.  
* Check Closed alerts over time and Net resolve rate.  
* Share whether you're closing alerts faster than new ones are being created.

**Q6: "How effective is push protection? Are developers bypassing it?”**

* Navigate to **Secret scanning insights**.  
* Report how many secrets were blocked versus bypassed, and the most common reason for bypass.

### Step 3 - (Optional) Deeper Dive

If you finish early, explore one or more of these areas:

* **Export data** – Use the Export CSV option to download current alert data for offline reporting.  
* **Pull request alerts** – In **Overview** -> **Prevention**, review how many vulnerabilities were prevented in PRs vs. introduced.  
* **Requests to bypass push protection** – If delegated bypass is enabled, check the Requests tab for open approvals.

## Exercise 11 - Signed SBOM

Frameworks like NIST SSDF, SLSA, and the EU Cyber Resilience Act (CRA) all emphasize software supply chain transparency. Common requirements across them include:

* NIST SSDF (PS.3.2): Collect and share provenance (SBOM).  
* SLSA: Produce signed attestations for build provenance.  
* CRA (Annex I Part II(1)): Mandate a machine-readable SBOM for all top-level dependencies.

The point is to give software consumers confidence that:

* The SBOM really came from the project's build system (authentic).  
* The SBOM hasn't been tampered with (integrity).  
* The SBOM describes exactly what was in the build (traceability).

In this exercise, we'll explore how to accomplish these goals all with the help of GitHub Actions, Artifact Attestations, and the GitHub CLI.

### Step 1 - Generate an SBOM with the GitHub API

We'll be working in the **mona-gallery-universe** repo for this exercise. Start by adding the following step at the end of the **release-attestation.yml** GitHub Actions workflow file to create an SBOM for each container image:

```
- name: Generate SBOM for built image
  uses: anchore/sbom-action@v0.20.5
  with:
    image: ${{ env.IMAGE_PREFIX }}/${{ matrix.service.name }}@${{ steps.push.outputs.digest }}
    format: cyclonedx-json
    output-file: ${{ matrix.service.name }}-sbom.cdx.json
    upload-artifact: false
    upload-release-assets: false
```

### Step 2 - Sign & Attest the SBOM

Build on the same workflow file by adding a step to sign the SBOM and link it to your container image:

```
- name: Attest SBOM
  uses: actions/attest-sbom@v3.0.0
  with:
    subject-name: ${{ env.IMAGE_PREFIX }}/${{ matrix.service.name }}
    subject-digest: ${{ steps.push.outputs.digest }}
    sbom-path: ${{ matrix.service.name }}-sbom.cdx.json
    push-to-registry: true
```

Open a pull request and merge these new changes into the `main` branch.

### Step 3 - Create a release

Next, create a **v1.0.0** tag to trigger a release:

* Option 1: From the CLI  
  * Clone the repo locally: `git clone https://github.com/ORG/REPO.git`  
  * Create a tag: `git tag v1.0.0`  
  * Push the tag to the remote repository: `git push origin v1.0.0`  
* Option 2: From the UI  
  * Next to the `main` branch dropdown, click on **0 tags**  
  * Select **Create a new release** (if you don't see this button, select the **Releases** tab)  
  * In the **Select tag** dropdown, type **v1.0.0**, then select **Create new tag: v1.0.0**  
  * Select **Publish release**

Navigate to the Actions tab to ensure a workflow was triggered by the new tag. **While the workflow runs, complete Step 4\.**

### Step 4 - Connect to [ghcr.io](http://ghcr.io) locally

Create a personal access token with the **read:packages** scope:

* Navigate to [https://github.com/settings/tokens](https://github.com/settings/tokens)   
* Click **Generate new token** -> **Generate new token (Classic)**  
* Select the **read:packages** checkbox  
* Click the **Generate token** button  
* Copy the token value to your clipboard

In your terminal, run the following command to authenticate to [ghcr.io](http://ghcr.io):

```
docker login ghcr.io -u <your_handle>@github.com
```

When prompted, paste in the personal access token. This will allow the GitHub CLI to reach out to [ghcr.io](http://ghcr.io) on your behalf to fetch the container image details.

### Step 5 - Verify the attestation

For easier copy/paste throughout the next few steps, set an environment variable equal to your organization name:

```
export GITHUB_ORG=<YOUR_ORG_NAME>
```

*Pause* - ensure that the Actions workflow from earlier has completed successfully.

Confirm the origin details for one of the newly released container images, **auth v1.0.0**:

```
gh attestation verify oci://ghcr.io/$GITHUB_ORG/auth:1.0.0 \
  --owner $GITHUB_ORG \
  --predicate-type https://cyclonedx.org/bom
```

Review the SBOM details for the newly released **auth v1.0.0** container image:

```
gh attestation verify oci://ghcr.io/$GITHUB_ORG/auth:1.0.0 \
  --owner $GITHUB_ORG \
  --predicate-type https://cyclonedx.org/bom \
  --format json \
  --jq '.[].verificationResult.statement.predicate'
```

This exercise moves the storyline from **scanning code for vulnerabilities** to **proving your supply chain is trustworthy**. You've now established the foundation for **transparent and verifiable software consumption**.
