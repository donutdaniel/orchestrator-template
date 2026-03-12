# AgentSpec Bundles

Canonical `v2alpha1` bundle shapes for common workspace setups. These shapes are the reference
contract for starter repositories such as `dho-labs/orchestrator-template` and its successors.

Every bundle must include exactly one `Workspace` resource at `agentspec/workspace.yaml`.

```yaml
apiVersion: agentspec.orchestrator.dev/v2alpha1
kind: Workspace
metadata:
  key: workspace
spec:
  git:
    defaultAutonomyMode: human-review
  toolPolicy:
    preset: supervised
```

## Single-Repo Product Workspace

Use when one product repo owns the application and the default executor handles nearly all work.

```text
agentspec/
├── workspace.yaml
├── repos/
│   └── app.yaml
├── environments/
│   └── dev.yaml
├── agents/
│   ├── orchestrator.yaml
│   ├── delegator.yaml
│   └── executor.yaml
├── workflows/
│   └── default.yaml
└── automations/
    └── triage-issues.yaml
```

Recommended defaults:

- Use `workspace.yaml` for shared git defaults, task defaults, tool policy, and prune policy
- One `Repo` with `autonomyMode: human-review`
- One default `Environment` with the minimum required secret keys
- One default `Agent` per role
- One `Workflow` that keeps verification as an explicit final step

## Multi-Repo Engineering Workspace

Use when orchestration spans several product or service repositories and repo-level autonomy varies.

```text
agentspec/
├── workspace.yaml
├── repos/
│   ├── api.yaml
│   ├── web.yaml
│   └── infra.yaml
├── environments/
│   ├── dev.yaml
│   └── staging.yaml
├── agents/
│   ├── orchestrator.yaml
│   ├── delegator.yaml
│   ├── executor.yaml
│   └── executor-thorough.yaml
├── workflows/
│   ├── default.yaml
│   └── incident-fix.yaml
└── automations/
    └── incident-intake.yaml
```

Recommended defaults:

- Use `workspace.yaml` for shared autonomy defaults and reusable permission rules
- Set repo-specific autonomy on each `Repo`
- Use step-level `agentKey` overrides in `Workflow` for verification or review-heavy phases
- Keep connector credentials in environment secrets, never in connector bindings
