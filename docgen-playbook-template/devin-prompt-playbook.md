
# 🚀 Devin Prompt Playbook for System + Component Documentation

This playbook helps you generate **best-in-class DeepWiki-style documentation** across 28+ microservices and supporting repositories.

---

## 🔹 Step 1 – Quick Draft Prompt

Use this when you want Devin to generate a **first draft** quickly.

```
Generate a DeepWiki-style system documentation across these 38 repos:
[INSERT REPO NAMES HERE]

Cover system overview, end-to-end flows, service dependency graph, data contracts, infra/CI-CD, security, observability, NFRs, runbook, glossary/ownership, cost, release/versioning, and roadmap, with Mermaid diagrams where applicable.
```

---

## 🔹 Step 2 – Iterative Refinements

After the draft, you can refine specific sections. Example follow-up prompts:

- “Expand the **Observability** section with details on logging, metrics, and distributed tracing.”  
- “Add **dependency graphs** for Orders, Payments, and Inventory flows.”  
- “Include **changelog and known issues per repo**.”  
- “Add **deployment details** including Kubernetes manifests and Helm charts.”  
- “Expand **Security** to cover IAM, secrets, and compliance policies.”

---

## 🔹 Step 3 – Final Polished Prompt (Gold Standard)

Use this once you’re ready for the **final, best-practice documentation**.

```
You have access to 38 repositories that together form our system (28 core microservices + 10 supporting repos for infra, CI/CD, shared libs, and monitoring).

Generate best-practice **DeepWiki-style documentation** that would be considered “gold standard” technical documentation.

Structure the documentation as follows:

1. System-Level Docs
   - System overview (functional + technical)
   - End-to-end flows with sequence diagrams
   - Service dependency graph
   - Shared data contracts & schemas
   - Deployment & infrastructure (CI/CD, IaC, orchestration)
   - Security & compliance
   - Observability & monitoring
   - Non-functional requirements (scalability, SLAs, DR)
   - Operational runbook
   - Glossary & ownership map
   - Cost/resource considerations
   - Release/versioning strategy
   - Roadmap

2. Component-Level Docs (for each repo in the list below)
   - Overview
   - Architecture & design (with diagrams)
   - API documentation
   - Database/data models
   - Key sequence diagrams
   - Dependencies
   - Configuration/environment
   - Error handling/retry
   - Test coverage/completeness
   - Performance & scaling
   - Dev setup guide
   - Deployment/CI-CD
   - Security
   - Observability hooks
   - Known issues & TODOs
   - Changelog
   - Ownership/contacts

Repos to include:
[INSERT REPO LIST HERE]

Output the documentation in navigable DeepWiki style, with cross-links between system-level and component-level pages. Use **Mermaid diagrams** inline in Markdown for all architectures, flows, and dependencies.
```

---

## 🔹 Best Practices When Using Devin

- Start with the **Quick Draft** to see what Devin can generate automatically.  
- Use **Iterative Refinements** to fill gaps or expand sections.  
- Finish with the **Gold Standard Prompt** to produce a polished, comprehensive document.  
- Always include the **repo list inline** so Devin knows the full scope.  
- Use **Mermaid diagrams** for visual clarity and easy rendering in Markdown/DeepWiki.  

---

✅ With this playbook, your team can efficiently move from **draft → refine → final** and ensure documentation stays **consistent, comprehensive, and navigable** across all 38 repositories.
