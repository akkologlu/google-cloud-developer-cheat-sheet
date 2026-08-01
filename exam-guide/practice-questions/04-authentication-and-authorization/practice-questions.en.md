# Module 4 — Authentication and Authorization: Practice Questions

Scenario-style practice questions for **Handling Authentication and Authorization**, weighted toward the distinctions the exam loves to test: which principals can actually authenticate, which auth method fits which deployment environment, the ADC lookup order, and the Workload Identity vs Workload Identity Federation naming trap.

Try to answer every question before checking the answer key. Source material: `deep-dive/04-authentication-and-authorization/authentication-and-authorization.md`.

---

## Questions

**1.** A team lead grants the `roles/storage.objectViewer` role to the Google group `data-analysts@company.com` so all 30 analysts on the team get read access to a bucket in one step. One analyst later asks whether she can skip her own login and have a backend script authenticate an API request directly "as" the group, to save a step. Is this possible?

A. Yes — Google Groups can generate their own OAuth access tokens.
B. No — Google Groups have no login credential; they only simplify bulk permission grants, so each analyst must still authenticate with her own Google Account.
C. Yes, but only if the group owner enables MFA on the group.
D. No — only service accounts, never Google Accounts, can be added to a Google Group.

**2.** A startup does not subscribe to Google Workspace — its 50 employees have no Gmail, Docs, or Drive access through the company. The startup still wants to manage all of its employees' Google Accounts centrally and apply IAM policies at the organization level. Which principal type fits this need?

A. Google Workspace account
B. Cloud Identity domain
C. Google group
D. A single shared service account

**3.** A security review of a production project finds that most engineers were granted the basic **Viewer** role at the project level so they could inspect BigQuery datasets. This also gives them read access to every other resource type in the project. What should the security team do to align with least privilege?

A. Leave it as is — Viewer is read-only, so it carries no real risk in production.
B. Replace the basic Viewer role with a predefined or custom role scoped only to the BigQuery permissions the engineers actually need.
C. Upgrade everyone to Editor so they can also fix issues they find.
D. Keep Viewer, but compensate by enabling VPC Service Controls.

**4.** A nightly batch job runs unattended on a Compute Engine VM and calls the Cloud Storage API to write export files. No human is present during the run. Which mechanism should authorize these API calls?

A. An API key embedded in the job's configuration file.
B. A user account OAuth token obtained by running `gcloud auth login` once on the VM.
C. A service account representing the batch job, with IAM roles scoped to only the Storage permissions it needs.
D. A Firebase Authentication ID token.

**5.** A company discovers that a downloaded service account private key was accidentally committed to a public repository. Before they caught it, an attacker used the key to grant the service account (and thus themselves) an additional, more powerful role. The company immediately deletes the leaked key. Are they now safe?

A. Yes — deleting the key permanently revokes every action the attacker could take.
B. No — this is privilege escalation: the extra IAM role the attacker granted stays in effect even after the leaked key is revoked, so the company must also audit and manually remove any bindings the attacker created.
C. Yes, because service account keys automatically expire the moment a leak is detected.
D. No, but only because Secret Manager was not used to store the key.

**6.** A developer writes a Python script that runs on her laptop and calls the Cloud Vision API through the client library, without specifying any credentials in code — she is relying on Application Default Credentials. Which command must she run first so the script can authenticate?

A. `gcloud auth login`
B. `gcloud auth application-default login`
C. `gcloud config set account`
D. `gcloud iam service-accounts keys create`

**7.** A Cloud Run service has a user-managed service account attached to it. The environment variable `GOOGLE_APPLICATION_CREDENTIALS` also happens to be set, pointing at a service account JSON file baked into the container image by mistake during testing. Which credential does Application Default Credentials actually use at runtime?

A. The attached service account, because attached service accounts always take priority over everything else.
B. The service account key file referenced by `GOOGLE_APPLICATION_CREDENTIALS`, because ADC checks that environment variable first, before falling back to gcloud user credentials or an attached/default service account.
C. Whichever credential was configured most recently wins.
D. ADC throws an error whenever more than one credential source is present.

**8.** A production application runs on a Compute Engine VM and needs to call the Cloud SQL Admin API. What is the recommended way to give it credentials without managing key files?

A. Download the VM's default service account JSON key and set `GOOGLE_APPLICATION_CREDENTIALS` to point at it.
B. Attach a purpose-built service account, scoped to only the permissions the app needs, directly to the VM.
C. Run `gcloud auth application-default login` once on the VM and leave the resulting file in place.
D. Embed a Cloud SQL API key in the VM's startup script.

**9.** Several microservices run as separate pods in the same GKE cluster. Today they all inherit the broad permissions of the cluster's node service account. The team wants each microservice to have its own distinct, fine-grained IAM identity instead. What should they configure?

A. Workload Identity Federation, since it works for any external workload.
B. Workload Identity, mapping each microservice's Kubernetes service account to its own dedicated IAM service account.
C. A single service account key mounted into every pod as a Kubernetes Secret.
D. A separate API key per microservice.

**10.** Part of a company's CI/CD pipeline runs on AWS (CodeBuild), and those AWS jobs need to publish artifacts to Artifact Registry in Google Cloud. The company wants to avoid managing any long-lived service account keys, and AWS can produce an OIDC-compatible identity token for each job. What should they configure?

A. Workload Identity, since it works for any workload outside a Google Cloud project.
B. A downloaded service account key stored in AWS Secrets Manager and rotated manually.
C. Workload Identity Federation, exchanging the AWS-issued token for a short-lived Google Cloud access token, with no service account key involved.
D. `gcloud auth application-default login` run inside the CodeBuild agent.

**11.** An architecture review document states: *"We'll use Workload Identity so our on-premises Jenkins server can impersonate a Google Cloud service account without needing a key file."* Is this the correct approach and terminology?

A. Correct as written.
B. Incorrect — Jenkins is running outside Google Cloud (on-premises), so this scenario calls for Workload Identity Federation; Workload Identity specifically refers to GKE workloads impersonating an IAM service account.
C. Incorrect — on-premises workloads have no keyless option at all and must always use a downloaded service account key.
D. Correct, but only if Jenkins is later migrated to run inside a GKE cluster.

**12.** A legacy on-premises system cannot perform OIDC-based federation, so the team must issue it a service account key as a last resort. Following the recommended best practice, how should they generate this key to minimize risk?

A. Generate the key pair in the Google Cloud Console the normal way and download the resulting private key.
B. Generate their own public/private key pair on their own infrastructure, upload only the public key to Google Cloud, and deliver the private key to the runtime environment through their own secure process.
C. Embed the downloaded private key directly into the application binary so it ships as one artifact.
D. Store the private key in a Git repository that is only accessible to internal employees.

**13.** An internal HR web application hosted on Google Cloud needs to be reachable over HTTPS from the public internet, but access to certain pages must be restricted to HR managers specifically. The team wants to avoid standing up a VPN and avoid writing custom authorization code inside the app. What should they use?

A. VPC firewall rules that allowlist specific source IP ranges.
B. A traditional VPN that all HR staff must connect through.
C. Identity-Aware Proxy (IAP), which enforces identity- and policy-based access control at the application level without requiring in-app authorization code.
D. A distinct API key issued to each HR manager.

**14.** A company originally built its mobile app's login screen with Firebase Authentication. A new enterprise customer now requires SAML-based single sign-on and multi-factor authentication for their users, and the company also wants to protect an internal admin portal with Identity-Aware Proxy. What should they do?

A. Stay on Firebase Authentication and simply enable an MFA add-on.
B. Migrate to Identity Platform, which builds on the same foundation as Firebase Authentication but adds OIDC/SAML sign-in, MFA, and IAP integration for enterprise needs.
C. Switch to a Cloud Identity domain instead of any authentication product.
D. Configure only an OAuth 2.0 consent screen; no other change is required.

**15.** A compliance requirement states that a database password stored as a secret must (a) keep its data only in EU regions, (b) be encrypted with keys the organization controls, and (c) have every read logged for audit. Which configuration satisfies this in Secret Manager?

A. Use the `automatic` replication policy, rely on the default IAM setup (project owners only), and skip extra encryption — Secret Manager handles everything by default.
B. Use a `user-managed` replication policy restricted to EU regions, encrypt the secret version with Cloud KMS, and ensure Cloud Audit Logs is enabled.
C. Skip Secret Manager entirely and store the password as a plain environment variable, since that is simpler to reason about.
D. Use the `automatic` replication policy and grant read access to all authenticated users for convenience.

---

## Answer Key & Explanations

**1. Correct answer: B.** Google Groups (and Google Workspace accounts and Cloud Identity domains) have no login credential of their own — they exist purely to let you apply IAM bindings to many principals at once. The tempting distractor is thinking "if a group can hold a role, it must be able to act," but only Google Accounts and service accounts can actually authenticate a request.

**2. Correct answer: B.** A Cloud Identity domain is the virtual group for organizations that want centralized identity management without being a Google Workspace customer — its users specifically lack access to Workspace apps like Gmail, Docs, and Drive. Google Workspace account is the tempting-but-wrong choice because it looks identical ("virtual group of the whole org"), but it implies Workspace app access, which this startup doesn't have.

**3. Correct answer: B.** Basic roles like Viewer are broad by design — they grant read access to every resource type in the project, not just the one the team actually needs, violating least privilege. The distractor "Viewer is read-only, so it's safe" is exactly the trap: read-only doesn't mean scoped, and excess read access is still excess attack surface (e.g., exposing configs, secrets metadata, or other teams' data).

**4. Correct answer: C.** A service account is built for exactly this case: an unattended, machine-to-machine call, authorized through IAM roles rather than a person's identity. API keys are the tempting-but-wrong option because they look convenient, but a leaked API key gives full, indefinite access and most Google APIs don't even accept them for this kind of write operation.

**5. Correct answer: B.** This is the classic privilege-escalation risk: once an attacker uses a leaked key to grant additional IAM bindings, those bindings are independent of the key itself, so rotating or deleting the key does not undo them. The tempting-but-wrong answer is "deleting the key fixes it" — that stops future use of the key, but it does not claw back the standing access the attacker already granted itself.

**6. Correct answer: B.** `gcloud auth application-default login` is specifically for feeding Application Default Credentials so that code (not the CLI) can authenticate as the developer. The trap is `gcloud auth login`, which looks almost identical and is used for authenticating the `gcloud` CLI itself — it does not populate ADC.

**7. Correct answer: B.** ADC's lookup order checks the `GOOGLE_APPLICATION_CREDENTIALS` environment variable first; only if it's unset does ADC fall back to gcloud user credentials, then an attached service account, then the service's default service account. The trap is assuming "attached service account always wins" because it's the recommended production pattern — but if a stray env var is set (as in this accidental-baked-in-testing-file scenario), it silently takes priority, which is exactly why leftover env vars are a real production bug.

**8. Correct answer: B.** Attaching a purpose-built service account directly to the VM is the preferred way to supply credentials to production workloads — Google manages the credential lifecycle, and no key file ever needs to be downloaded, copied, or rotated. The distractor (download and set `GOOGLE_APPLICATION_CREDENTIALS`) reintroduces exactly the key-management risk (leakage, manual rotation) that attached service accounts exist to eliminate.

**9. Correct answer: B.** Workload Identity is the GKE-specific mechanism that lets a Kubernetes service account impersonate an IAM service account, giving each workload its own fine-grained identity instead of sharing the node's broad service account. Workload Identity Federation is the tempting-but-wrong distractor because of the near-identical name, but it's designed for workloads running outside Google Cloud, not for pods inside a GKE cluster.

**10. Correct answer: C.** Workload Identity Federation exists precisely for this case: an external workload (outside Google Cloud) that can produce an OIDC-compatible token exchanges it for a short-lived Google Cloud access token, eliminating the need for a service account key entirely. The trap is picking "Workload Identity" by name-similarity — that mechanism only applies inside GKE clusters, not to AWS-hosted workloads.

**11. Correct answer: B.** This is the textbook Workload Identity vs. Workload Identity Federation mix-up: Workload Identity is scoped to GKE (Kubernetes service account impersonating an IAM service account), while any workload running outside Google Cloud — including on-premises — needs Workload Identity Federation instead. Distractor C is wrong because federation is exactly the keyless option that exists for on-premises workloads that support OIDC.

**12. Correct answer: B.** The recommended best practice when a service account key truly can't be avoided is to generate your own key pair, upload only the public key to Google Cloud, and keep the private key entirely within your own secure delivery process — this way Google never even handles your private key. The distractor "download the key normally" is the default, most common path, but it's precisely the higher-risk one the best practices are warning against.

**13. Correct answer: C.** IAP is built for exactly this: centralized, identity-aware, application-level access control over HTTPS, without VPNs, network firewall rules, or custom authorization code inside the app. The VPN option is tempting because it's the traditional way to gate internal apps, but it works at the network level, not per-user/per-page, and the team explicitly wants to avoid it.

**14. Correct answer: B.** Identity Platform is Firebase Authentication plus the enterprise layer — OpenID Connect/SAML sign-in, multi-factor authentication, and IAP integration — built on the same underlying foundation, so migrating preserves existing investment. The distractor "just enable an MFA add-on" is wrong because Firebase Authentication itself doesn't offer SAML sign-in or IAP integration; those specifically require Identity Platform.

**15. Correct answer: B.** A `user-managed` replication policy lets you pin secret data to specific regions (satisfying data residency), Cloud KMS integration lets you encrypt secret versions with organization-controlled keys, and Cloud Audit Logs records every read and write for compliance. The `automatic` replication distractor is tempting because it's the simpler default, but it explicitly allows the payload to be stored in any region, which fails the EU-only requirement.
