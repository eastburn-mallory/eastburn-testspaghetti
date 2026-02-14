# Environment Secret Proof of Concept

## Overview

This repository demonstrates how GitHub Actions environments and environment-level secrets can be used securely within a workflow.

The workflow contains two jobs:

1. **Input Processing**
   - Consumes a manually provided input.
   - Prints the input to the workflow logs.
   - Produces a job-level output.

2. **Secret Validation**
   - Runs within a GitHub Actions environment.
   - Validates that an environment secret is configured.
   - Prints the output produced by the first job.

This implementation mirrors how engineers receive and complete structured work assignments while adhering to clearly defined acceptance criteria.

---

## Workflow Structure

### Trigger

The workflow is triggered manually using `workflow_dispatch`.  
When running the workflow, the user must provide a text input named `message`.

---

### Job 1: Input Processing

- Runs on `ubuntu-22.04`
- Prints the provided input
- Generates an output named `consumed_input`
- Does **not** use a GitHub Actions environment

This demonstrates how to pass data between jobs using step outputs and job outputs.

---

### Job 2: Secret Validation

- Runs on `ubuntu-22.04`
- Attached to the `dev` environment
- Validates that the environment secret `FAVORITE_HERO` is populated
- Does not expose the secret value
- Prints the output received from Job 1

This demonstrates secure usage of environment-scoped secrets and cross-job data sharing.

---

## How to Set up and Run This Workflow

This section assumes no prior experience with GitHub Actions.

### Step 1: Create the Repository
<sup>Official Github Reference Documentation: [Creating a new repository](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository)</sup>

1. In the top right corner of any GitHub page, select the plus icon, and choose **New repository**.
2. Enter **Repository name** in the following format.
3. (Optional) Add a description to your new repository to briefly describe the purpose.
4. Under configuration, ensure the following is selected:
    - Choose visibility: **Public**
    - Add README: **On**
    - *Note: For this assessment, it is not necessary to add a .gitignore or a license.*
5. Select **Create repository** to create.

---

### Step 2: Create the Environment and add an Environment Secret
<sup>Official Github Reference Documentation: [Managing environments for deployment](https://docs.github.com/en/actions/how-tos/deploy/configure-and-manage-deployments/manage-environments?search-overlay-input=environments)</sup>

1. While viewing your repository, go to **Settings** -> **Environment**.
2. Select **New environment** and name it, and click *Configure environment**.
   > Example environment name: **dev**
3. While in the environment, select **Add environment secret**.
4. Name the secret and provide the value for the secret.
5. Save the secret.

---

### Step 3: Create the Workflow
<sup>Official Github Reference Documentation: [Creating an example workflow](https://docs.github.com/en/actions/tutorials/create-an-example-workflow)</sup>
                                                                                                                                                                                                                                                                                      >
1. In your repository, on the <> Code tab, select **Add file** -> **Create new file**.
2. Name your file the following: **.github/workflows/<your_first_name>-<favorite_animal>.yaml**
    > Example file name: **.github/workflows/mallory-wolf.yaml**
4. Add the workflow configurations to the newly created file.

---

### Step 4. Run the Workflow
<sup>Official Github Reference Documentation: [Manually running a workflow](https://docs.github.com/en/actions/how-tos/manage-workflow-runs/manually-run-a-workflow)</sup>

1. While viewing the repository, go to **Actions** tab.
2. From the left navigation bar, select the workflow you previous created.
3. Click **Run workflow**.
4. Provide a value for the message input.
5. Select **Run workflow**.

---

## Security Measures Implemented

The workflow includes the following security controls:

### 1. Restricted Workflow Permissions

```yaml
permissions:
  contents: read
```

The default GitHub token is restricted with read-only access.

This follows the principle of least privilege and prevents unnecessary write access.

---

### 2. Concurrency Control

```yaml
concurrency:
  group: environment-secret-poc
  cancel-in-progress: true
```
This ensures that only one workflow run executes at a time.

If a new run is triggered, any in-progress run is automatically canceled.

This prevents race conditions and unnecessary resource usage.

---

### 3. Job Timeouts

```yaml
timeout-minutes: 5
```

Each Job has a maximum execution time.

This prevents runaway processes or stalled jobs from consuming resources indefinitely.

---

## Future Security Enhancements

While this implementation satisfies the defined acceptance criteria and incorporates multiple security controls, additional improvements could be introduced in a production setting.

### 1. Input Validation and Sanitization

The current workflow accepts free-form text input.  
In a production workflow, input validation could be expanded to:

- Restrict length limits
- Enforce allowed character patterns
- Sanitize special characters to prevent shell injection risks

This would further reduce the attack surface when handling user-supplied input.

---

### 2. Shell Hardening

Each step currently uses the default shell configuration.  
Security could be strengthened by explicitly enforcing:

```bash
set -euo pipefail
```

---
### Branch Protection

- Require pull requests for changes to main branch.
- Require at least one approving review.
- Enforce status check before merging.

