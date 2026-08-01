# Practice Questions — Module 6: Deploying Applications

Scenario-based practice questions for the **Deploying Applications** module (CI/CD pipeline anatomy, Software Delivery Shield, containers, and Cloud Build). Based on [`deep-dive/06-deploying-applications/deploying-applications.md`](../../../deep-dive/06-deploying-applications/deploying-applications.md).

Try to answer every question before checking the answer key — that's where the actual exam-prep value is.

---

## Questions

**1.** Your team wants fast feedback on code quality: every time a developer commits to their own feature branch, the code should automatically compile and run unit tests — with no deployment anywhere, not even to a test environment. Which part of the pipeline does this describe?

A) Continuous Deployment
B) Continuous Delivery
C) Continuous Integration
D) A Binary Authorization policy check

**2.** After a change is pushed to the `main` branch, your pipeline builds the app, deploys it to staging, runs integration and performance tests, and tags a successful build as a release candidate. A release manager must still click "approve" before that build goes out to production as a canary release. What is this practice called?

A) Continuous Integration
B) Continuous Deployment
C) Continuous Delivery
D) Blue-green release

**3.** Same pipeline as above, but leadership now wants to remove the approval step entirely: as long as staging tests pass, the release candidate should roll out to production with no human clicking anything. What does this change turn the pipeline into?

A) Continuous Delivery
B) Continuous Deployment
C) Continuous Integration
D) Canary release only, with no delivery stage

**4.** A team ships a new version by initially routing only 5% of production traffic to it, watches Cloud Monitoring for errors, and gradually increases the percentage if everything looks healthy. What release strategy is this?

A) Blue-green release
B) Canary release
C) Continuous deployment
D) A rolling VM replacement

**5.** A team keeps two identical, fully-provisioned production environments running. To release, they cut all traffic from the old environment to the new one at once; if something goes wrong, they instantly flip traffic back to the old environment. What release strategy is this?

A) Canary release
B) Blue-green release
C) Continuous integration
D) A rolling update

**6.** Your team wants Assured OSS to give them Google-verified, continuously scanned versions of their open-source dependencies — including the npm packages used by their Node.js services. Will Assured OSS cover those npm packages?

A) Yes, Assured OSS covers all major package ecosystems, including npm
B) No — Assured OSS only covers Java and Python open-source packages
C) Yes, but only if the packages are also deployed to GKE
D) No, because Assured OSS was superseded by Binary Authorization

**7.** A security reviewer wants proof that a specific container image sitting in Artifact Registry was actually produced by your trusted CI pipeline from the approved source repository — not tampered with or substituted along the way. Which capability directly provides this proof?

A) Artifact Analysis vulnerability scan results
B) Cloud Build's verifiable build metadata
C) A Binary Authorization policy, on its own
D) The GKE security posture dashboard

**8.** You want every image sitting in Artifact Registry to be re-evaluated automatically whenever a new CVE is published — not just scanned once at push time. You are not asking for anything to be blocked, just kept up to date and visible. Which service does this, and what should you be careful not to assume it does?

A) Binary Authorization — it also blocks any image found to be vulnerable
B) Artifact Analysis — it scans on-demand and automatically, and keeps monitoring stored images for new vulnerabilities, but it does not by itself block anything from being deployed
C) Cloud Deploy — it blocks vulnerable images from being promoted to any target environment
D) Assured OSS — it continuously rescans every package in Artifact Registry, including your own internal ones

**9.** Your security team's policy is: only images that carry attestations proving they passed both a vulnerability scan and integration testing may be deployed to GKE. Any image missing those attestations must be actively prevented from running. Which service enforces this?

A) Artifact Analysis
B) Cloud Build build metadata
C) Binary Authorization
D) A Cloud Monitoring alerting policy

**10.** A developer says: "We already use Cloud Build to compile our app and produce the Docker image, so let's also use Cloud Build to sequence the rollout across staging and production, with one-click approval and rollback." Is Cloud Build the right tool for that second part?

A) Yes — Cloud Build natively handles both building images and sequencing multi-environment deployments
B) No — Cloud Build's job ends at building, testing, and pushing the image to Artifact Registry; sequencing deployments across target environments with one-click approval and rollback is Cloud Deploy's job
C) No — sequenced deployments with approval/rollback must be scripted manually with `gcloud`; no managed service offers this
D) Yes, but only if Binary Authorization is disabled first

**11.** A colleague suggests: "Let's just turn on Software Delivery Shield and we're covered end to end — one API call and the whole pipeline is secured." Is this an accurate description of what Software Delivery Shield is?

A) Yes — it is a single managed API that replaces Assured OSS, Cloud Build, Artifact Analysis, Cloud Deploy, and Binary Authorization
B) No — Software Delivery Shield is an umbrella term for the combination of Assured OSS, Cloud Build's build metadata, Artifact Analysis, Cloud Deploy, Binary Authorization, and GKE/Cloud Run runtime security; it is not a single standalone tool you switch on
C) No — the term only refers to Binary Authorization's policy engine
D) Yes, but it is available only for GKE workloads, not Cloud Run

**12.** A junior engineer says: "A container is basically just a smaller, faster VM — each container still runs its own operating system, just a lighter one." What is wrong with this statement?

A) Nothing — that's an accurate description, containers just use a trimmed-down OS image
B) Containers virtualize at the operating system level, using process isolation and namespaces on a single shared kernel; unlike a VM, a container does not carry its own OS copy
C) Containers virtualize hardware exactly like VMs do, they just boot faster because of caching
D) Containers require a hypervisor the same way VMs do

**13.** A developer builds and tests a container image on their laptop. The exact same image is then promoted to an integration environment, and later to production, without being rebuilt or repackaged at any point. Which container benefit does this scenario best illustrate?

A) Application isolation
B) Workload portability
C) Separation of responsibility
D) Binary Authorization compliance

**14.** While editing a Cloud Build configuration file, an engineer needs to know: (1) what determines which container is invoked to actually run a given build step, and (2) what determines the name of the final container image the whole build produces. Which two fields matter here?

A) `steps` and `substitutions`
B) `name` (the container invoked for that step) and `images` (the name of the image produced by the build)
C) `source` and `target`
D) `trigger` and `tag`

**15.** A Cloud Build pipeline has three steps, each running in its own container: step 1 downloads dependencies, step 2 compiles the app using those dependencies, and step 3 packages the compiled output into a Docker image. How does the output of one step become available to the next step?

A) Each step manually uploads its output to a Cloud Storage bucket configured in the pipeline
B) Cloud Build mounts the `/workspace` directory into every step's container, and files placed there persist and are available to subsequent steps
C) Steps communicate by exporting shell environment variables to each other
D) Every step's output must first be pushed to Artifact Registry before the next step can read it

---

## Answer Key & Explanations

**1. C — Continuous Integration.** CI is triggered by a commit to a feature branch and consists of automatic build + test, with the resulting artifact stored (e.g., in Artifact Registry) — no deployment happens at this stage. The trap is confusing this with Delivery: Delivery is triggered by a push to `main` and includes deploying to staging, not just compiling and testing a feature branch.

**2. C — Continuous Delivery.** The giveaway is the manual approval before production: Delivery automates everything up to producing a release-ready, tested build, but a human still decides when it actually goes to production. Continuous Deployment (B) is the tempting-but-wrong answer because the rest of the pipeline (staging test, release candidate, canary) is identical — only the approval step differs.

**3. B — Continuous Deployment.** Removing the manual approval gate is the entire distinction between Delivery and Deployment; everything else about the pipeline (build, staging tests, release candidate tagging, canary/blue-green rollout) stays the same. This is the module's most explicitly called-out exam trap: look for the phrase "no human intervention" vs. "I approve the release candidate."

**4. B — Canary release.** Canary means exposing a new version to a small percentage of traffic first and ramping up gradually so that any problem affects a limited slice of users rather than everyone. Blue-green (A) is the common distractor, but blue-green is an all-at-once cutover between two identical environments, not a gradual percentage ramp.

**5. B — Blue-green release.** The defining traits are two identical environments and an instant, total traffic cutover (with an equally instant rollback by flipping back). Canary (A) is the distractor because both strategies are about safe production releases, but canary ramps gradually while blue-green switches all at once.

**6. B — No, only Java and Python.** Assured OSS explicitly covers Java and Python open-source packages that Google builds through its secure pipelines and continuously scans — it does not extend to npm, Go modules, or other ecosystems. This is an easy-to-miss scope limitation, since people assume "open source security service" means "all languages."

**7. B — Cloud Build's verifiable build metadata.** This metadata is what lets you prove an artifact came from a trusted source and a trusted build process — essentially a "certificate of origin." Binary Authorization (C) is the tempting distractor because it also deals with trust, but on its own Binary Authorization enforces policy using attestations; it doesn't generate the underlying build provenance the way Cloud Build's metadata does.

**8. B — Artifact Analysis, and it does not block anything.** Artifact Analysis provides on-demand and automatic scanning of stored images plus continuous monitoring for newly discovered vulnerabilities — but it is purely an observation/reporting layer. The trap is answer A: people often assume a vulnerability scanner also blocks deployment, but that enforcement role belongs to Binary Authorization, not Artifact Analysis.

**9. C — Binary Authorization.** Binary Authorization collects attestations (proof that an image passed required steps like scanning or testing), checks them against your organization's policy, and actively prevents non-compliant images from being deployed. Artifact Analysis (A) is the classic wrong answer here — it can tell you an image has a vulnerability, but it cannot stop that image from being deployed.

**10. B — No, that's Cloud Deploy's job.** Cloud Build's role is building, testing, and pushing the image to Artifact Registry; Cloud Deploy is the service that automates sequenced delivery to a series of target environments with one-click approval, rollback, and security insights. Confusing the two is an easy mistake since both have "Cloud" and "Build/Deploy" in the name and both sit in the same pipeline.

**11. B — It's an umbrella term, not a single tool.** Software Delivery Shield is Google's name for the combination of Assured OSS, Cloud Build metadata, Artifact Analysis, Cloud Deploy, Binary Authorization, and GKE/Cloud Run runtime security working together — there is no single switch that "is" Software Delivery Shield by itself. Treating it as one monolithic product is the trap; each underlying service still has to be understood and configured on its own terms.

**12. B — Containers are OS-level virtualization, not tiny VMs.** A VM virtualizes hardware and each VM runs its own full OS copy, which is why VMs are slow to boot (~1 minute or more) and resource-heavy. A container virtualizes the operating system itself, using process isolation and namespaces on one shared kernel, which is why containers start in a fraction of a second and are lightweight. This exact "container = mini VM" misconception is explicitly flagged as an exam trap in the source material.

**13. B — Workload portability.** The scenario — same image, unmodified, moving from laptop to integration to production — is exactly the definition of workload portability: containers are lightweight enough to run almost anywhere with minimal effort to promote them through the lifecycle. Application isolation (A) is the distractor because it's also a real container benefit, but it refers to isolating dependencies/resources between containers on the same host, not to moving one image across environments unchanged.

**14. B — `name` and `images`.** In a Cloud Build configuration file, each step is itself a Docker container invocation, and the `name` attribute defines which container image is invoked to run that step; the `images` attribute (top-level) specifies the name of the container image the build ultimately produces and pushes. These two fields are easy to conflate since both relate to "images," but they answer different questions — which container runs the step vs. what image comes out of the build.

**15. B — The `/workspace` directory.** Cloud Build mounts the source code into `/workspace` inside each step's container, and anything a step writes there persists and is available to every subsequent step — even though each step runs in its own isolated container. This is what makes multi-step pipelines (download dependencies → compile → package) work without each step needing external storage just to hand off files.
