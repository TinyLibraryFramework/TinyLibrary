# TinyLibrary Project Requirements
## General Instructions for setting up the project and management tools
* We are going to create a collaborative project framework using the TinyLibrary project folder, based on the contents of the Tiny Library.pdf file
* TinyLibrary will be an open source public facing development project, with its own github organization, that embodies the values and principles in the background documents.
* We need to keep the development of the framework and the development of the tools decoupled
* We need to create all needed project management artifacts including md files and skills
* One important project artifact will be a governing document for the project, how tools are build to work in the framework, what testing is required, coding styles and standards, API documentation, and similar related concerns.
* We also need to create a shared Claude Project to enable local collaboration using claude code.
* A key decision will be choice of programming languages. The TinyLibrary back end platform will be hosted on Linux OS systems at a range of scale, from embedded Linux on application processors to scalable multi-core cloud servers.
* Git flow should be used with branches for features and bug fixes
* Github project management should be used to track feature progress
* Semantic versioning should be used for the framework and individual tools
* A system will be needed for code documentation
* A system will be needed for user manuals

## Additional Project Considerations

### Licensing
* For an open-source project with its own GitHub org, the license choice (MIT, Apache 2.0, GPL, etc.) shapes everything from contribution terms to how downstream users can integrate TinyLibrary. This should be decided early.

### Community and Contribution Guidelines
* A CONTRIBUTING.md, code of conduct, and PR review process will be needed. As a public-facing project, there should be clear expectations for how outside contributors engage.

### CI/CD Pipeline
* Automated testing, linting, and builds on every PR. Especially important given the range of target platforms (embedded ARM through cloud x86).

### Security Practices
* A vulnerability reporting process, dependency scanning, and a policy for how security patches are handled. Critical for anything running on embedded systems.

### Architecture Decision Records (ADRs)
* A lightweight system to document the "why" behind key architectural and design choices. These age much better than comments or tribal knowledge.

### Target Platform Specifications
* Define which Linux kernel versions, CPU architectures (ARM, RISC-V, x86_64), and minimum resource profiles are supported. "Embedded to cloud" is broad, and the boundaries matter for language and dependency choices.

### Backwards Compatibility and Deprecation Policy
* Especially important for a framework — a clear policy for how APIs evolve without breaking downstream tools, and how deprecations are communicated and phased out.

### Testing Strategy
* Define the kinds of testing required (unit, integration, end-to-end, performance), coverage expectations, and how testing works across the target platform range.

### Release Cadence and Changelog Practices
* Establish how often releases happen, how changes are communicated via changelogs, and how migration guides are provided for breaking changes.

### Dependency Management Philosophy
* A policy for how third-party dependencies are vetted, approved, and kept minimal — which matters significantly on embedded targets with constrained resources.
