# Petclinic DevSecOps Pipeline

## Architecture
GitHub → Jenkins → Maven → Trivy → JFrog Artifactory → Docker

## Features
- CI pipeline with Jenkins
- Secure artifact storage in Artifactory
- SBOM generation (CycloneDX)
- Vulnerability scanning (Trivy)
- Docker image build & push
- Build metadata & traceability

## Key Learnings
- Jenkinsfile-as-code
- Docker container networking
- Secure credential handling
- Supply chain security
``
