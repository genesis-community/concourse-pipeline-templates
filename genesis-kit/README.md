## Settings Configuration

This document explains how to fill out the `settings.yml` file for configuring the pipeline.

### Structure

The `settings.yml` file is structured into several sections, each containing specific configuration parameters.

#### Meta Section

The `meta` section contains general information about the kit and the pipeline.

```yaml
meta:
  kit:     <kit-name>
  release: <release-name>
  target:  <target-name>
  url:     <pipeline-url>
  iaas:    <infrastructure>
  exposed: <true|false>
  initial_version: <version>
```

- `kit`: The name of the kit.
- `release`: The name of the release.
- `target`: The target name for the pipeline.
- `url`: The URL of the pipeline.
- `iaas`: The infrastructure type (e.g., `vsphere`, `aws`).
- `exposed`: Whether the pipeline is exposed to the public (`true` or `false`).
- `initial_version`: The initial version of the release.

### Upstream Section

The `upstream` section contains information about the upstream manifest and BOSH releases.

```yaml
upstream:
  manifest:
    package: <package-name>
    repo:    <repository-url>
    path:    <path>
    filter:
    - <filter-paths>

  bosh_releases:
  - name:       <release-name>
    type:       <release-type>
    repository: <repository-url>
    path:       <path>
    access_token: ((<access-token>))
```

- `manifest`: Information about the upstream manifest.
  - `package`: The package name.
  - `repo`: The repository URL.
  - `path`: The path to the manifest.
  - `filter`: A list of paths to include in the manifest.

- `bosh_releases`: A list of BOSH releases.
  - `name`: The name of the release.
  - `type`: The type of the release (e.g., `github-release`).
  - `repository`: The repository URL.
  - `path`: The path to the release file.
  - `access_token`: The access token for accessing the repository.

#### BOSH Section

The `bosh` section contains information about the BOSH environment.

```yaml
bosh:
  uri:      ((<bosh-uri>))
  username: ((<bosh-username>))
  password: ((<bosh-password>))
  cacert:   ((<bosh-ca-cert>))
```

- `uri`: The URI of the BOSH environment.
- `username`: The username for accessing the BOSH environment.
- `password`: The password for accessing the BOSH environment.
- `cacert`: The CA certificate for the BOSH environment.

#### Vault Section

The `vault` section contains information about the Vault environment.

```yaml
vault:
  url:       ((<vault-url>))
  token:     ((<vault-token>))
```

- `url`: The URL of the Vault environment.
- `token`: The token for accessing the Vault environment.

#### AWS Section

The `aws` section contains information about the AWS environment.

```yaml
aws:
  access_key: ((<aws-access-key>))
  secret_key: ((<aws-secret-key>))
```

- `access_key`: The access key for accessing the AWS environment.
- `secret_key`: The secret key for accessing the AWS environment.

#### GitHub Section

The `github` section contains information about the GitHub repository.

```yaml
github:
  owner:        <github-owner>
  repo:         <github-repo>
  branch:       <branch-name>
  main-branch:  <main-branch-name>
  private_key:  ((<github-private-key>))
  access_token: ((<github-access-token>))
  name:         <github-name>
  email:        <github-email>
```

- `owner`: The owner of the GitHub repository.
- `repo`: The name of the GitHub repository.
- `branch`: The branch name for the repository.
- `main-branch`: The main branch name for the repository.
- `private_key`: The private key for accessing the repository.
- `access_token`: The access token for accessing the repository.
- `name`: The name associated with the GitHub account.
- `email`: The email associated with the GitHub account.

### Shout Section

The `shout` section contains information about the Shout! notification service.

```yaml
shout:
  url:      ((<shout-url>))
  username: ((<shout-username>))
  password: ((<shout-password>))
```

- `url`: The URL of the Shout! notification service.
- `username`: The username for accessing the Shout! notification service.
- `password`: The password for accessing the Shout! notification service.

### Example

Here is an example of a complete `settings.yml` file:

```yaml
meta:
  kit:     cf-app-autoscaler
  release: CF App Autoscaler Genesis Kit
  target:  pipes/genesis
  url:     https://pipes.scalecf.net
  iaas:    vsphere
  exposed: no
  initial_version: 1.0.0

  upstream:
    manifest:
      package: app-autoscaler
      repo:    cloudfoundry/app-autoscaler-release
      path:    upstream
      filter:
      - templates/*
      - operations/*

    bosh_releases:
    - name:       app-autoscaler
      type:       github-release
      repository: cloudfoundry/app-autoscaler-release
      path:       overlay/releases/app-autoscaler.yml
      access_token: ((github.access_token))

  bosh:
    uri:      ((bosh.uri))
    username: ((bosh.username))
    password: ((bosh.password))
    cacert:   ((bosh.ca))

  vault:
    url:       ((vault.url))
    token:     ((vault.token))

  aws:
    access_key: ((cloudfoundry-community-aws.access_key_id))
    secret_key: ((cloudfoundry-community-aws.secret_access_key))

  github:
    owner:        genesis-community
    repo:         cf-app-autoscaler-genesis-kit
    branch:       develop
    main-branch:  main
    private_key:  ((github.private_key))
    access_token: ((github.access_token))
    name:         ((github.name))
    email:        ((github.email))

  shout:
    url:      ((shout.url))
    username: ((shout.username))
    password: ((shout.password))
```

## Pipeline Components

### Scripts Directory

The `scripts` directory contains various scripts used for building, testing, and deploying the pipeline. Here is a brief overview of each script:

| Script | Purpose |
|--------|---------|
| `build-kit` | Compiles and packages the Genesis kit |
| `check-sha1s` | Validates SHA1 checksums of releases |
| `compare-release-specs` | Compares release specifications between versions |
| `deploy` | Deploys environment for verification |
| `generate-release-notes` | Creates formatted release notes |
| `get-latest-upstream-manifest` | Fetches current upstream manifest |
| `release` | Manages GitHub releases and tags |
| `release-notes` | Generates notes from commit history |
| `spec-check` | Validates release specifications |
| `test-addons` | Tests deployment add-ons |
| `test-deployment` | Verifies deployment functionality |
| `update-release` | Updates to latest release version |

There are also scripts that are used to build and upload the concourse pipelines.  These are:

| Script | Purpose |
|--------|---------|
| `../repipe` | Updates Concourse pipeline configuration |
| `build-test-jobs` | Creates test pipeline jobs from templates |
| `build-upstream-jobs` | Generates upstream pipeline integration jobs |

### Tasks Directory

The `tasks` directory contains various task definitions used in the pipeline. Here is a brief overview of each task:

| Task | Purpose |
|------|---------|
| `pipeline/jobs/build-kit.yml` | Kit build configuration |
| `tasks/deploy.yml` | Environment deployment steps |
| `tasks/deploy-stable.yml` | Stable environment deployment |
| `tasks/generate-release-notes.yml` | Release notes generation |
| `tasks/get-latest-upstream-manifest.yml` | Upstream manifest retrieval |
| `tasks/prerelease.yml` | Pre-release creation steps |
| `tasks/release.yml` | Release creation process |
| `pipeline/jobs/spec-check.yml` | Specification validation |
| `pipeline/optional-jobs/spec-tests.yml` | Specification testing |
| `tasks/update-release.yml` | Release update process |
| `tasks/upgrade.yml` | Environment upgrade steps |
