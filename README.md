# Aqua-Demo

GitHub Release Test Go Report Card License: Apache-2.0 GitHub All Releases Docker Pulls

📖 Documentation

Trivy (tri pronounced like trigger, vy pronounced like envy) is a comprehensive security scanner. It is reliable, fast, extremely easy to use, and it works wherever you need it.

Trivy has different scanners that look for different security issues, and different targets where it can find those issues.

Targets:

Container Image
Filesystem
Git repository (remote)
Kubernetes cluster or resource
Scanners:

OS packages and software dependencies in use (SBOM)
Known vulnerabilities (CVEs)
IaC misconfigurations
Sensitive information and secrets
Much more scanners and targets are coming up. Missing something? Let us know!

Read more in the Trivy Documentation

Quick Start
Get Trivy
Get Trivy by your favorite installation method. See installation section in the documentation for details. For example:

apt-get install trivy
yum install trivy
brew install aquasecurity/trivy/trivy
docker run aquasec/trivy
Download binary from https://github.com/aquasecurity/trivy/releases/latest/
General usage
trivy <target> [--security-checks <scanner1,scanner2>] TARGET_NAME
Examples:

$ trivy image python:3.4-alpine
Result
$ trivy fs --security-checks vuln,secret,config myproject/
