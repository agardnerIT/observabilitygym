# ObservabilityGym

> AI has ExploitGym. Monitoring has ObservabilityGym.

A vendor-neutral, reproducible benchmark for evaluating observability
backends — the missing "cross-vendor cutting" test that the AI ecosystem has
and the observability ecosystem does not.

Today the closest thing observability has is the Gartner Magic Quadrant:
analyst judgment, opaque scoring, not rerunnable by the customer.
ObservabilityGym is the reproducible measurement layer — the same telemetry,
the same workloads, the same probes, run against every vendor, ranked by
falsifiable numbers anyone can re-run.

## The idea in one paragraph

ExploitGym benchmarks opaque hosted AI vendors (Anthropic, OpenAI, Google)
with a pinned harness, and the AI labs accept those rankings.
ObservabilityGym adapts that proven protocol — determinism, fresh
per-campaign secrets, an independent verifier, a strict submission and
leaderboard process — to observability vendors, and adds a fully-controlled
tier ExploitGym cannot have.

## Two tiers

| | Field | Controlled |
|---|---|---|
| Targets | Hosted SaaS: Datadog, Honeycomb, New Relic, Grafana Cloud | Self-hosted: SigNoz, Grafana OSS stack, Jaeger + Prometheus, Splunk Enterprise, Elastic, Dynatrace Managed … |
| Environment | We control our side only; conditions recorded in `metadata.yml` | Everything pinned — hardware, versions, config |
| Result type | Distributions + confidence intervals | Point values |
| Claim | *Experienced* service quality | *Engine* capability |

"Tier" is a property of the target adapter, not a fork of the project.

## Docs

- [Methodology](docs/METHODOLOGY.md) — the ExploitGym protocol, tier split,
  dimensions, tolerance bands, anti-gaming rules, non-goals, roadmap

## Status

**Phase 0** — methodology agreed, harness skeleton next. See the
[roadmap](docs/METHODOLOGY.md#6-roadmap).

## License

Apache-2.0 — the same license, and the same discipline, as ExploitGym.