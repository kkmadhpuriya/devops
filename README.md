# GitHub Actions Reusable CI Workflow

This repository contains a GitHub Actions Reusable Workflow designed to replace the legacy Jenkins CI pipeline. It supports building and pushing Docker containers for projects such as ReactJS, NextJS, NodeJS, and Spring.

## Components

- **`.github/workflows/deploy.yml`**: The main reusable GitHub Actions CI workflow for building Docker images and publishing them to the registry.

## Usage

In your project repository, create a workflow file (e.g., `.github/workflows/ci.yml`) that calls this reusable workflow.

### Example Caller Workflow

```yaml
name: CI Pipeline

on:
  push:
    branches:
      - main

jobs:
  build-app:
    uses: kkmadhpuriya/jenkins-global-pipeline/.github/workflows/deploy.yml@main
    with:
      project_path: "frontend"
      app_type: "next"
      node_version: "18"
      build_env: "prod"
      domain_with_path: false
```

## Inputs Configuration

| Input              | Description                                                         | Required | Default |
| ------------------ | ------------------------------------------------------------------- | :------: | ------- |
| `project_path`     | Directory of the project code (e.g. `frontend` or `.`)              |   Yes    | -       |
| `app_type`         | Type of app (`react`, `next`, `node`, `spring`, `react-native`)     |   Yes    | -       |
| `node_version`     | Node version to use during checks                                   |    No    | `18`    |
| `build_env`        | Environment (e.g., `prod`, `dev`)                                   |   Yes    | -       |
| `api_base_url`     | API base URL for frontends (`REACT_APP_BASE_URL` & `API_BASE_URL`)  |    No    | `""`    |
| `domain_with_path` | If the domain includes a path prefix matching the project directory |    No    | `false` |
| `build_args`       | Additional raw build arguments for Docker                           |    No    | `""`    |

_Note on Docker Image Names:_ The docker image tag is automatically generated in the format:
`registry.k303.hostingbeta.com/github_org/github_repo-project_path-branch:latest`

## Pipeline Lifecycle

1. **Checkout & Testing**: Checks out the caller repo, sets up Node.js, and runs `npm audit` if relevant.
2. **Build & Push**: Uses standard Docker context to build and inject `BUILD_ENV` arguments, and pushes the container.
