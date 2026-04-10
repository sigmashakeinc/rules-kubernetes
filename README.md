# rules-kubernetes

Kubernetes manifest governance rules for AI coding agents. Prevents privileged containers, root user execution, hardcoded secrets in env vars, wildcard RBAC, missing health probes, and untagged images — enforcing Kubernetes security best practices at the agent tool-call boundary.

**16 rules · 3 files**

![rules-kubernetes — AI agent Kubernetes governance demo](demo.cast)

> [▶ Watch interactive demo on SigmaShake Hub](https://hub.sigmashake.com/ruleset/rules-kubernetes)


## Install

```bash
ssg hub pull rules-kubernetes
```

Available on the [SigmaShake Hub](https://hub.sigmashake.com) — the open registry for AI agent governance rules. Compatible with Claude Code, GitHub Copilot, Cursor, Windsurf, Aider, and any AI coding agent using the `ssg` hook protocol.

## Rules

### k8s_write_security.rules — Pod security (6 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `no-privileged-pod` | DENY | error | Blocks `privileged: true` — host root access |
| `no-run-as-root-uid` | DENY | error | Blocks `runAsUser: 0` — container root |
| `no-host-network` | DENY | error | Blocks `hostNetwork: true` — bypasses NetworkPolicy |
| `no-missing-resource-limits` | ASK | warning | Requires CPU/memory limits on containers |
| `no-secret-in-env-literal` | DENY | error | Blocks hardcoded secrets in env values |
| `no-latest-image-tag` | DENY | error | Requires pinned image tags |

### k8s_write_rbac.rules — RBAC least privilege (5 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `no-wildcard-rbac-verbs` | DENY | error | Blocks wildcard `*` RBAC verbs |
| `no-wildcard-rbac-resources` | DENY | error | Blocks wildcard `*` RBAC resources |
| `no-cluster-admin-binding` | DENY | error | Blocks ClusterRoleBinding to cluster-admin |
| `no-default-service-account` | ASK | warning | Flags use of default service account |
| `no-automount-service-account-token` | ASK | warning | Flags `automountServiceAccountToken: true` on low-trust workloads |

### k8s_write_probes.rules — Reliability & scheduling (5 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `no-missing-liveness-probe` | ASK | warning | Containers without liveness probe |
| `no-missing-readiness-probe` | ASK | warning | Containers without readiness probe |
| `no-host-port` | ASK | warning | Blocks `hostPort` — prefer Service resources |
| `no-aggressive-failure-threshold` | LOG | warning | failureThreshold of 1–2 causes unnecessary restarts |
| `no-single-replica-deployment` | LOG | warning | Single-replica Deployment has no HA during disruptions |

## Why this matters

AI agents writing Kubernetes manifests regularly produce configurations with `privileged: true`, `runAsUser: 0`, hardcoded secrets in `env[].value`, and `:latest` image tags — all of which violate Kubernetes security best practices and CIS Kubernetes Benchmark. Wildcard RBAC verbs (`*`) grant excessive cluster permissions that violate the principle of least privilege.

These rules enforce the Kubernetes security baseline at the agent tool-call level — catching misconfigurations before `kubectl apply` runs against a cluster.

## Compatible with

- Kubernetes 1.20+
- YAML manifests (`.yaml`, `.yml`)
- Works alongside kube-score, Polaris, and OPA/Gatekeeper — these rules operate at the agent tool-call level, not at admission time

## About

Part of the [SigmaShake Hub](https://hub.sigmashake.com) — open-source governance rules for AI coding agents.
Install the `ssg` CLI to enforce these rules: `npm install -g @sigmashake/ssg`
