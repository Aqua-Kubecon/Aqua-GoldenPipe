# Aqua-Demo

<div align="center">
<img src="docs/imgs/logo.png" width="200">

[![GitHub Release][release-img]][release]
[![Test][test-img]][test]
[![Go Report Card][go-report-img]][go-report]
[![License: Apache-2.0][license-img]][license]
[![GitHub All Releases][github-all-releases-img]][release]
![Docker Pulls][docker-pulls]

[📖 Documentation][docs]
</div>

This is a demo of how to use Aqua Enterprise Security and Trivy Open Source in the CI!

Aqua has different *scanners* that look for different security issues, and different *targets* where it can find those issues.

Scanners:
- OS packages and software dependencies in use (SBOM)
- Known vulnerabilities (CVEs)
- IaC misconfigurations
- Sensitive information and secrets

## Quick Start

### Create Github Action

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
        env:
          AQUA_KEY: ${{ secrets.AQUA_KEY }}
          AQUA_SECRET: ${{ secrets.AQUA_SECRET }}
          TRIVY_RUN_AS_PLUGIN: 'aqua'
          GITHUB_TOKEN: ${{ github.token }}
```
The "ENV" environment variables will use us to configure Aqua Enterprise

### Create Pull Request with vulnerability or misconfiguration 

You can download a few examples from this repository
- Requirements.txt (Open source vulnerabilities)
- Insecure-db.tf (Terraform misconfiguration)
- IaC-secrets.tf (Secret detection)

Find out more in the [Trivy Documentation][docs] - [Getting Started][getting-started]


## Highlights

- Comprehensive vulnerability detection
  - OS packages (Alpine Linux, Red Hat Universal Base Image, Red Hat Enterprise Linux, CentOS, AlmaLinux, Rocky Linux, CBL-Mariner, Oracle Linux, Debian, Ubuntu, Amazon Linux, openSUSE Leap, SUSE Enterprise Linux, Photon OS and Distroless)
  - **Language-specific packages** (Bundler, Composer, Pipenv, Poetry, npm, yarn, Cargo, NuGet, Maven, and Go)
  - High accuracy, especially [Alpine Linux][alpine] and RHEL/CentOS
- Supply chain security (SBOM support)
  - Support CycloneDX
  - Support SPDX
- Misconfiguration detection (IaC scanning) 
  - Wide variety of security checks are provided **out of the box**
  - Kubernetes, Docker, Terraform, and more
  - User-defined policies using [OPA Rego][rego]
- Secret detection
  - A wide variety of built-in rules are provided **out of the box**
  - User-defined patterns
  - Efficient scanning of container images
- Simple
  - Available in apt, yum, brew, dockerhub
  - **No pre-requisites** such as a database, system libraries, or eny environmental requirements. The binary runs anywhere.
  - The first scan will finish within 10 seconds (depending on your network). Consequent scans will finish instantaneously.
- Fits your workflow
  - **Great for CI** such as GitHub Actions, Jenkins, GitLab CI, etc.
  - Available as extension for IDEs such as vscode, jetbrains, vim
  - Available as extension for Docker Desktop, Rancher Desktop
  - See [integrations] section in the documentation.

---

Aqua Security Website: [Aqua Security][aquasec].
Learn about our Software Supply Chain Security Solution: [SupplyChain][SupplyChain].  
Contact us about any matter by opening a GitHub Discussion [here][discussions]


[getting-started]: https://aquasecurity.github.io/trivy/latest/getting-started/installation/
[docs]: https://aquasecurity.github.io/trivy
[integrations]:https://aquasecurity.github.io/trivy/latest/docs/integrations/
[installation]:https://aquasecurity.github.io/trivy/latest/docs/getting-started/installation/
[releases]: https://github.com/aquasecurity/trivy/releases
[alpine]: https://ariadne.space/2021/06/08/the-vulnerability-remediation-lifecycle-of-alpine-containers/
[rego]: https://www.openpolicyagent.org/docs/latest/#rego
[aquasec]: https://aquasec.com
[oss]: https://www.aquasec.com/products/open-source-projects/
[discussions]: https://github.com/aquasecurity/trivy/discussions
[SupplyChain]: https://www.aquasec.com/products/software-supply-chain-security/
