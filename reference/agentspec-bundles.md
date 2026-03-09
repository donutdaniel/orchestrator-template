# AgentSpec Bundles

Canonical `v2alpha1` bundle shapes for common workspace setups. These shapes are the reference
contract for starter repositories such as `donutdaniel/orchestrator-template` and its successors.

## Single-Repo Product Workspace

Use when one product repo owns the application and the default executor handles nearly all work.

```text
agentspec/
├── repos/
│   └── app.yaml
├── environments/
│   └── dev.yaml
├── agents/
│   ├── orchestrator-default.yaml
│   └── executor-default.yaml
├── tools/
│   ├── browser-navigate.yaml
│   └── repo-summary.yaml
├── workflows/
│   └── default.yaml
└── automations/
    └── daily-triage.yaml
```

Recommended defaults:

- One `Repo` with `autonomyMode: human-review`
- One default `Environment` with the minimum required secret keys
- One default `Agent` per role
- One `Workflow` that keeps verification as an explicit final step

## Multi-Repo Engineering Workspace

Use when orchestration spans several product or service repositories and repo-level autonomy varies.

```text
agentspec/
├── repos/
│   ├── api.yaml
│   ├── web.yaml
│   └── infra.yaml
├── environments/
│   ├── dev.yaml
│   └── staging.yaml
├── agents/
│   ├── orchestrator-default.yaml
│   ├── delegator-default.yaml
│   ├── executor-default.yaml
│   └── executor-thorough.yaml
├── connectors/
│   ├── linear.yaml
│   └── sentry.yaml
├── tools/
│   ├── linear-search-issues.yaml
│   ├── sentry-search-events.yaml
│   └── browser-navigate.yaml
├── workflows/
│   ├── default.yaml
│   └── incident-fix.yaml
└── automations/
    └── incident-intake.yaml
```

Recommended defaults:

- Set repo-specific autonomy on each `Repo`
- Use step-level `agentKey` overrides in `Workflow` for verification or review-heavy phases
- Keep connector credentials in environment secrets, never in connector bindings

## Production Ops Workspace

Use when the workspace exists to triage incidents, alerts, and operational regressions under tighter
network and tool controls.

```text
agentspec/
├── environments/
│   └── prod.yaml
├── agents/
│   ├── orchestrator-default.yaml
│   └── executor-default.yaml
├── connectors/
│   ├── pagerduty.yaml
│   ├── datadog.yaml
│   └── slack.yaml
├── tools/
│   ├── pagerduty-list-incidents.yaml
│   ├── datadog-search-logs.yaml
│   └── slack-post-message.yaml
├── workflows/
│   └── incident-response.yaml
└── automations/
    ├── pagerduty-high-urgency.yaml
    └── datadog-p1.yaml
```

Recommended defaults:

- `Environment.networkPolicy.egressMode: allowlist`
- `Environment.secrets.requiredKeys` for every provider credential needed by the workflow
- Agent and environment tool policies that default to `ask` or `deny` for write paths
- `Automation` targets pinned to the production environment and the incident workflow
