# Golden Pipeline Template

This is a template, leveraging [Trivy] and Aqua [Supply Chain Security] to build a **golden pipeline**.

We define a **golden pipeline** as a development pipeline (from source code management, through build and deploy) that is reliable, quick, and secure by default. Think  golden image, then shift it left. 

![Flow v2](https://user-images.githubusercontent.com/84018957/195580736-3bede00b-bb35-4b12-b8b2-f141c28b6776.png)

The template below is based on open-source [Trivy] scanners, which scan for:
 - OS packages and software dependencies in use (SBOM)
 - Known vulnerabilities (CVEs)
 - IaC misconfigurations
 - Sensitive information and secrets

We complement that with our enterprise [Supply Chain Security] features, which allow us to scale and automate based on:
 - **In-workflow alerts and remediations**, including comments on source code management and ability to warn / fail the build based on the defined policy
 - Be granular thanks to group-based **assurance policies**, **suppression rules** which are automatically enforced
 - Analyze **open-source health**, grading each package based on quality, maintainability, popularity and risk
 - Automatically generate, store and manage **next-gen SBOM**s, which contain the entire security history and integrity of each release artifact
 - Establish baseline pipeline secure configuration with **CI/CD posture management**
 - **Templatize**, through automatic mapping of new repos and integration of wanted scans and policies
 - Generate management-level **reports** of overall security, quality and compliance
 
 
## Quick Start

Our golden pipeline has three main stages, which is how we break down the subsections below:

### SCM
Integrate scanner to your SCM and enable automated scans to happen on a periodic schedule. 

This way, you:
- Establish a secure baseline, and
- Ensure that you're able to detect anything in your existing codebase suddently turning risky / vulnerable.

We leverage our enterprise features, as they have native integration to all the major SCM providers. This is how we auto-map all repos and run periodic scans on the background. 

### Pull request
Integrate scanner as a step on the CI, so that the scan is automatically triggered upon each developer pull request. You can use the command below:

```
name: Aqua
on: pull_request
jobs:
  aqua:
    name: trivy
    runs-on: ubuntu-18.04
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Run Trivy vulnerability scanner in IaC mode
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          security-checks: 'vuln,config,secret'
          hide-progress: false
          format: 'table'
```
If you would like to upload SARIF results to GitHub code scanning, you can switch the format to 'sarif' and add the following step to the above:
```
          format: 'sarif'
          output: 'trivy-results.sarif'

      - name: Upload Trivy scan results to GitHub Security tab
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'
```          

We leverage our enterprise features to automatically add comments to developers on the SCM, with feedback on the vulnerability / risk identified and how to clear it. 
For certain critical teams and applications, we've leveraged the enterprise policy engine to automatically warn / fail merges based on specific checks relevant to those teams and applications.

### Build
Integrate scanner as a step on the CI, so that the scan is automatically triggered upon each developer push. You can use the command below:
```
name: Aqua
on: push
jobs:
  aqua:
    name: trivy
    runs-on: ubuntu-18.04
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Run Trivy vulnerability scanner in IaC mode
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          security-checks: 'vuln,config,secret'
          hide-progress: false
          format: 'table'
```
Once the image has been built, in order to automatically generate a basic SBOM listing out all of the dependencies and licenses used, you can integrate the following step on the CI:
```
name: Manifest Generation / SBOM
on:
  push:
    branches:
    - master
  pull_request:
jobs:
  build:
    name: Checks
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Run Trivy in GitHub SBOM mode and submit results to Dependency Snapshots
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          format: 'github'
          output: 'dependency-results.sbom.json'
          image-ref: '.'
          github-pat: '<github_pat_token>'
```
We leverage our enterprise features to automatically generate a next-gen SBOM, which also includes:
- Security history - Which security scans the artifact went through, and which ones it didn't pass
- The path of the artifact - Is it currently in prod, where it was built, which commits where a part of it, and which developer owns it
- The security posture of the pipeline - Were there minimum reviewers? Branch protections? MFA by the developer? and more

For certain critical teams and applications, we've leveraged the enterprise policy engine to automatically warn / fail build based on specific checks relevant to those teams and applications. 

### Branch protection policy
On top of securing the code itself with the three steps mentioned above, by enforcing our branch protection policy we can ensure that all code is not only scanned, but also reviewed and approved. 

An easy way to set branch protections at the org level is via [safe settings]. 

After defining our specific branch policy, we validate it using [chain-bench], which benchmarks the repo's configuration against best practices:
```
jobs:
  chain_bench_scan:
    runs-on: ubuntu-latest
    name: Test Job
    steps:
      - name: Chain Bench
        id: chain-bench
        uses: aquasecurity/chain-bench-action@v1.0.0
        with:
          repository-url: ${{ github.server_url }}/${{ github.repository }} 
          github-token: ${{ secrets.GITHUB_TOKEN }}
```
And then we use the github action output from the scan step "chain-bench-result" to get a JSON we can run rego/jq against
```
      - name: Echo
        run: echo ${{ steps.chain-bench.outputs.chain-bench-result }}
```

### Create a pull request with vulnerabilities or misconfigurations 
To see the results in action, you can download and use a few of the examples from this repository
- [requirements.txt] (Open source vulnerabilities)
- [insecure-db.tf] (Terraform misconfiguration)
- [iac-secrets.tf] (Secret detection)

---

Learn more about [Aqua Security]

Learn more about our Software [Supply Chain Security] Solution

[getting-started]: https://aquasecurity.github.io/trivy/latest/getting-started/installation/
[docs]: https://aquasecurity.github.io/trivy
[integrations]:https://aquasecurity.github.io/trivy/latest/docs/integrations/
[installation]:https://aquasecurity.github.io/trivy/latest/docs/getting-started/installation/
[releases]: https://github.com/aquasecurity/trivy/releases
[alpine]: https://ariadne.space/2021/06/08/the-vulnerability-remediation-lifecycle-of-alpine-containers/
[rego]: https://www.openpolicyagent.org/docs/latest/#rego
[aquasec]: https://aquasec.com
[Aqua Security]: https://aquasec.com
[oss]: https://www.aquasec.com/products/open-source-projects/
[discussions]: https://github.com/aquasecurity/trivy/discussions
[Supply Chain Security]: https://www.aquasec.com/products/software-supply-chain-security/
[Trivy]: https://github.com/aquasecurity/trivy
[requirements.txt]: https://github.com/Aqua-Kubecon/Aqua-Demo/blob/main/requirements.txt
[insecure-db.tf]: https://github.com/Aqua-Kubecon/Aqua-Demo/blob/main/insecure-db.tf
[iac-secrets.tf]: https://github.com/Aqua-Kubecon/Aqua-Demo/blob/main/iac-secrets.tf
[safe settings]: https://github.com/github/safe-settings
[chain-bench]: https://github.com/aquasecurity/chain-bench

