# Assignment: Build a CI Pipeline with GitHub Actions

### Part 1 – Repository Setup

Dockerfile:
![Photo](<Screenshots/dockerfile.png>)

### Part 2 – CI Workflow
Github Action:
![Photo](<Screenshots/github-action.png>)

### Part 3 – Secrets Management
Secrets Usage:
![Photo](<Screenshots/secrets-usage-1.png>)
![Photo](<Screenshots/secrets-usage-2.png>)

### Part 4 – Best Practices Questions
1. `kubectl apply` should not be applied in a CI pipeline as it is a CD (continuous deployment) method.
CI pipeline takes care of building and testing the application.
Using kubectl apply directly in CI tightly couples build and deployment, breaks separation of concerns, and violates GitOps principles. In modern workflows, CI produces artifacts, and CD tools handle deployment separately.

2. The `latest` docker tag is often used as the latest stable version tag.
`latest` is not a best practice docker tag as the latest image pushed is not always the latest stable.
It changes every time a new image is pushed and makes rollbacks difficult.
Using immutable tags (such as commit SHA or version numbers) ensures traceability, stability, and reproducibility.

3. CI means Continuous Integration, it is responsible for automatically integrating the application - building and testing and preparing it for deployment. It validates code quality and produces a deployable artifact.

4. This pipeline supports GitOps by testing the code, producing a deployable artifact using docker image, tags the artifact with an immutable tag to be used by a following CD pipeline.


### Bonus
#### Branch based image tags:
![Photo](<Screenshots/set-image-tag.png>)