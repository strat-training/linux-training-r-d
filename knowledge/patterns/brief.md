# M01 — Getting Started with Terraform Brief

**Stage:** 1 of 9 · **Builds on:** — (course entry point) · **Feeds into:** M02, M03, M08, M09

## Objective

By the end of this stage, you can set up your own workstation, sign in to AWS without static keys, and take one simple resource through the whole Terraform lifecycle — initialise, plan, apply, update and destroy — using your own terminal output and plans as the evidence, not a diagram from a slide. You can explain what Terraform is, what a provider is and why versions are pinned, and predict from a plan whether a change updates a resource in place or replaces it.

## Scope

You build up one small configuration file by file on your own machine, not a worked example. Find out, and write down in your own words:

- **Step 1 — The workstation (zero code)** — what Terraform is (declarative versus imperative), the core toolchain including a version manager, and keeping static access keys off disk; the environment and the mental model come before any editor. Research: what problem Terraform solves that a script or the cloud console does not and where you would not use it, how describing the desired state differs from scripting the steps, which install method fits each tool on each operating system and what each choice costs in maintenance and support, how a version manager keeps Terraform at the version you pinned, what fails differently on Windows than on macOS, how you prove you are connected to AWS without static keys, and what a scan for static keys has to look at and what it can miss.
- **Step 2 — Dependencies: the `versions.tf` file** — what a provider is, version constraints, dependency management and the lock file, introduced before any infrastructure is written. Research: what Terraform downloads when you initialise and where it puts it, the difference between a version constraint you declare and what the lock file records, how loose or tight a provider constraint should be and who carries the risk of each choice, and what a reviewer should look for in a merge request that changes the lock file.
- **Step 3 — Configuration: the `providers.tf` file** — provider concepts, how Terraform finds the AWS session you signed in with, and default tags, kept in a file of its own; checking syntax on a small, safe file before the configuration gets complex. Research: how the AWS provider decides which credentials to use and how you would prove which identity a plan is using, and why tags are set at the provider rather than on each resource and what would make you tag a resource individually anyway.
- **Step 4 — Resources and planning: the `main.tf` file** — the anatomy of a resource (type, name, arguments and attributes), and plan review as a habit. Research: which parts of a resource you set and which Terraform reports back, what to check in a plan before you approve it, how you can tell from the plan alone what a change will do, and what you would do if a plan showed a destroy you did not expect.
- **Step 5 — Apply and destroy** — in-place updates versus replacement, and destroying what you created. Research: which changes to your resource are made in place and which force replacement and how sure you are, what a replacement costs when the resource holds data or other resources depend on it, and how you confirm from the cloud side that everything is gone.

## Stack constraints

- Everything under [Rules for every stage](../../README.md#rules-for-every-stage) applies.
- You work on macOS or Windows, and your notes must serve a learner on either one.
- If you own only one of the two operating systems, agree with your trainer how the other will be verified.
- Terraform is 1.11 or later at the exact version in `versions.env`, and you use the Terraform CLI, not OpenTofu.
- You install Terraform through a version manager, `tfenv` or `asdf`, on the platforms where one works, and say in your notes where neither does.
- Your editor is Visual Studio Code with the HashiCorp Terraform extension, and the other tools the labs use are the AWS CLI, Git and `jq`; the labs' scripts are POSIX shell.
- You sign in to AWS with IAM Identity Center through the AWS CLI, and you use no IAM user access keys and no long-lived credentials of any kind.
- The AWS sandbox was provided before the course, so setting up or managing sandbox access, accounts, budgets or guardrails is not part of this stage.
- You use AWS only, with one very simple resource, an `aws_ssm_parameter`, so that you focus on the workflow and not on AWS architecture.
- State is local in this stage and is never committed, and you read the plan before every apply.
- Look up what the resource bills before you apply, and destroy it at the end of the stage.
- Variables and validation (M04), state and remote backends (M02), lifecycle controls that change replacement behaviour (M06), refactoring without replacement (M08), GCP and Azure (M09), and HCP Terraform, Terraform Stacks, Packer and Kubernetes providers are not part of this stage.

## Deliverable

**A configuration made of `versions.tf`, `providers.tf` and `main.tf` that manages one simple AWS resource, taken through create, in-place update and destroy from a workstation you set up yourself**, with a written plan-review note.

## Lab

**Goal.** Take one simple AWS resource through the whole Terraform lifecycle, from an empty folder to a clean destroy, on a workstation you set up yourself.

**You do, in the sandbox.**

1. The workstation: install Terraform through a version manager (`tfenv` or `asdf`) and the AWS CLI, configure VS Code, sign in to AWS through IAM Identity Center, and prove the connection without static keys with `aws sts get-caller-identity` and `terraform -version`.
2. Dependencies: create `versions.tf` with the required providers block, run `terraform init`, and watch the `.terraform` folder and the `.terraform.lock.hcl` file appear.
3. Configuration: create `providers.tf` with the AWS provider and a default tags block, then run `terraform fmt` and `terraform validate`.
4. Resources and planning: create `main.tf` with a single resource, run `terraform plan`, read the output, and check that the default tags from step 3 are attached to the resource in the plan.
5. Apply and destroy: run `terraform apply`, change one attribute to trigger an update, run `terraform plan` and look for the in-place update, apply again, then run `terraform destroy`.

**You build and capture.** The three files and the workstation are your deliverable. Capture the version output (including a switch between two Terraform versions), the caller-identity output, the scan for static keys, the files that `terraform init` created, the plan with the default tags on the resource, the plan that shows the in-place update, and the destroy confirmed from the cloud side.

**Clean-up.** Destroy the resource and confirm from the cloud side that it is gone.

## Definition of done

- DoD-01: Your notes show the actual Terraform version, installed through a version manager and switched between two versions (output pasted), and a caller-identity query from your own signed-in session (output pasted, account details redacted as the trainer directs), not the version or the identity you assumed you had.
- DoD-02: Your notes show the scan you ran for static access keys on disk and in your repository, and what it looked for, not a statement that there are none.
- DoD-03: `terraform init` created the `.terraform` folder and the `.terraform.lock.hcl` file from your own `versions.tf`, the lock file is committed (it appears in your commit), and your notes say in your own words what it recorded, not what the documentation says.
- DoD-04: Formatting and validation pass with no errors on your `providers.tf` and `main.tf`, shown as pasted output, not described from memory.
- DoD-05: Your plan output is pasted and read: it shows the default tags on your resource, and your plan-review note classifies every change you made as create, in-place update or replacement, and your trainer confirms each classification on review.
- DoD-06: The plan before your one-attribute change shows an in-place update (output pasted), and after destroy nothing exists in the sandbox, confirmed from the cloud side, not only from Terraform's output.
- DoD-07: Your write-up takes a first-time learner on either operating system from an empty folder to a destroyed resource unaided — verified by you following it end to end from a clean state.

## Best practices this stage demonstrates

- Version managers for Terraform (tfenv/asdf)
- Strict file compartmentalization (versions.tf, providers.tf)
- No static credentials
- Pinned versions, lock file
- `default_tags`
- fmt and validate
- Plan review as a habit

## Still open / ask your trainer

- How the operating system you do not own will be verified.
- Which details of the caller-identity output to redact before you paste it.
- Whether your sandbox lets you create an SSM parameter in your account and region.
- How much detail your notes need — confirm the expected length and depth with your trainer before you invest significant time.