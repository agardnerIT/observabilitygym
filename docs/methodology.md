# ObservabilityGym Methodology

> AI has ExploitGym. Monitoring has ObservabilityGym.

ObservabilityGym is a vendor-neutral, reproducible benchmark for evaluating
observability backends. It adapts the evaluation protocol that the AI
community accepted for [ExploitGym](https://github.com/sunblaze-ucb/exploitgym)
— where serious AI labs and providers agree on the rules — and applies it to
the observability ecosystem, which today relies on analyst judgment (e.g. the
Gartner Magic Quadrant) instead of auditable, reproducible measurement.

We are the reproducible measurement layer that analyst quadrants do not
provide. We measure what backends *do* with your telemetry, not how vendors
are positioned.

## 1. The ExploitGym precedent (why this protocol is credible)

ExploitGym is a large-scale benchmark that evaluates autonomous AI agents by
running them against controlled, pinned target environments. Key facts about
its design:

- **The harness is fully controlled and pinned** (Docker images, VMs,
  versions, secrets) — everything the benchmark owns is deterministic.
- **The thing under test is an opaque, hosted service.** Agents are driven by
  OpenAI, Anthropic, and Google models via remote APIs (Codex, Claude Code,
  Gemini CLI) through a budget-gated proxy and a firewall. The AI labs accept
  being ranked as black-box hosted APIs by a harness they do not control.
- **Results are published with caveats, not without them.** Provider-side
  variance in cost and latency is explicitly labeled as an estimate.
- An independent **`-results` repository** accepts submissions via pull
  request (`metadata.yml` + `results.json`), maintainers validate them, and
  validated runs enter a public leaderboard.

The inference for observability: **benchmarking opaque hosted vendors with a
pinned harness is an accepted, established pattern.** ObservabilityGym applies
that same pattern to observability vendors — and adds a fully-controlled tier
that ExploitGym cannot offer.

## 2. The two tiers — Field is first-class, Controlled is the differentiator

"Tier" is a property of the *target adapter*, not a different project. The
same harness, the same workloads, the same probes, and the same signing
apply to both. `--target signoz` and `--target datadog` differ only in the
adapter, exactly as ExploitGym swaps an endpoint and credentials rather than
forking its codebase.

| | Field tier | Controlled tier |
|---|---|---|
| What it measures | Hosted SaaS vendors (Datadog, Dynatrace, Honeycomb, New Relic, Grafana Cloud) | Self-hosted backends we provision ourselves |
| Environment | We control only our side (emit path, workload, queries, timing) | We control everything: hardware, OS, network, versions, config |
| Result type | Distribution + confidence intervals | Point values, ExploitGym-grade determinism |
| Claim supported | *Experienced* service quality | *Engine* capability |
| Methodology precedent | ExploitGym ranks opaque hosted APIs (OpenAI/Anthropic/Google) | Pinned-environment evaluation (ExploitGym's harness discipline) |

### Field tier (first-class)

Hosted vendors cannot pin their infrastructure for us: fleet, rollout,
retention, sampling configuration, and multi-tenant neighbor load vary run to
run. The protocol therefore measures and publishes honestly:

- Identical telemetry emitted over OTLP to each vendor.
- Identical workload seeds, probe queries, and timing.
- Every condition we can observe is recorded in `metadata.yml`: dates, times,
  regions, tenant identifiers, configurations, API limits.
- Results are reported as **distributions with confidence intervals**, not
  point claims.
- Publishing caveats is a *requirement*, not an afterthought. ExploitGym's
  acceptance came from publishing what it could not control; we do the same.

The claim a Field result supports is narrow and honest: *"what one customer
experienced from N locations against vendor X on these dates."* A small gap
between vendors is not conclusively attributable to capability — and the
methodology never says otherwise.

### Controlled tier (the differentiator)

Where we have the environment in our hands, we pin everything:

- Same machine class, OS, network, and clock for every backend under test.
- Backend software pinned to named versions with our configuration.
- The only variable between runs is the backend software itself, so
  differences are attributable to the engine.

Controlled is not open-source-only. Commercial vendors that ship
self-hostable or enterprise editions — Splunk Enterprise, Elastic, Grafana
Enterprise, Instana, and others — run in this tier with full laboratory
control. This recovers a large share of "rank the vendors people actually
buy" without any caveats.

## 3. What we measure

The telemetry we emit is the problem; the "flag" is whether a backend can
faithfully ingest, store, and return it. We do **not** trust vendor dashboards
— an independent verifier probes each backend's query API and diffs against
our stored ground truth.

| Dimension | What is measured | How |
|---|---|---|
| Fidelity | Traces reconstruct with correct tree, attributes, status, error codes; counters and histogram quantiles within tolerance; log fields, severity, timestamps intact; cross-signal correlation preserved | Verifier probes vs. signed ground truth |
| Latency | Emit → queryable, including p95/p99 tails | Timed probes |
| Query performance | Latency/correctness of typical retrievals (trace-by-id, aggregation, high-cardinality filters) | Pre-registered query set |
| Throughput & reliability | Sustained ingestion ceiling, drop rate under backpressure, burst behavior, recovery | Load generator |
| Standards fidelity | OTLP/OpenTelemetry semantics preserved (resource attributes, semantic conventions, sampling decisions) | Schema checks |

### Tolerance bands, not binary flags

ExploitGym's `flag_captured` is binary and its `on_target` scorer judges
causal necessity. Neither transfers: telemetry ingestion has no discrete
exploit path, and fidelity is a spectrum. We deliberately do **not** have an
`on_target` analog — it would be contested and fraudable.

Instead, every dimension is graded against **pre-registered tolerance bands**
published in the rubric *before* any run (e.g. attribute fidelity ≥ 99.5%,
histogram quantile error ≤ 1%, trace completeness ≥ 99%). This converts
contestable judgments into falsifiable criteria: a total pass/fail is replaced
by "met band / missed band by how much," and both the rubric and tolerance
bands are versioned and public.

## 4. Integrity and anti-gaming (the "vendors can't argue" clause)

The methodology's legitimacy rests on rules that make results auditable and
gaming impossible:

1. **Fresh per-campaign secrets.** Salt, signal seed, and API key are
   generated on every campaign and shared only via environment variables.
   **No constant is shipped in the repository.** (The analog of ExploitGym's
   controller secrets.)
2. **Signed ground truth.** Per-scenario expected results are derived from
   the campaign seed (HMAC), stored in the verifier's database, and compared
   against what the vendor actually returns.
3. **Mandatory full-suite runs.** Vendors cannot pick a subset; the canonical
   task list (`data/task_ids/v1.txt`) is the only valid submission shape.
4. **Repeated trials with confidence intervals.** No single-run numbers.
5. **Attested results.** A signed results file binds workload seed + target
   configuration + results, so submissions are verifiable and non-repudiable.
6. **Published probes and rubric.** Everything a vendor would need to dispute
   is public — which means disputes must be about *results*, not method.
7. **Version discipline.** Pinned benchmark versions, canonical task lists,
   and a CHANGELOG, exactly as ExploitGym (v1.0, 869 instances) maintains.

## 5. What we deliberately do NOT rank

The scope boundary is the objectivity guarantee. We do not rank: pricing and
cost, commercial support, UI/UX, feature breadth, or strategic vision. Those
are legitimate differentiators and remain the domain of analyst research
(e.g., the Magic Quadrant's "ability to execute" and "completeness of
vision"). ObservabilityGym and analyst quadrants are complementary: MQ answers
"who is well positioned?"; we answer "who faithfully stores and returns my
telemetry under conditions I can reproduce?" Both exist, but only one is
rerunnable by the customer.

## 6. Roadmap

| Phase | Deliverable | Tier |
|---|---|---|
| 0 | Methodology + harness skeleton (generator, verifier, driver, signing) | Both |
| 1 | Controlled ranking of ≥ 3 backends (SigNoz, Grafana OSS stack, Jaeger + Prometheus/VictoriaMetrics) | Controlled |
| 2 | Field pilot: one hosted vendor, clearly labeled *proof of workflow, not a ranking* | Field |
| 3 | Load / reliability / high-cardinality / soak dimensions | Both |
| 4 | Scoring engine, leaderboard, attestation, CI | Both |
| 5 | Field submissions open via the `-results` repository; distributed runner; SDK-language parity | Both |

## 7. Relationship to ExploitGym — the pitch, in one paragraph

ExploitGym benchmarks opaque hosted AI vendors (Anthropic, OpenAI, Google)
with a pinned harness, and the AI labs accept those rankings. ObservabilityGym
does the same for observability vendors — and adds a fully-controlled tier
ExploitGym cannot have. Vendors can read every rule, run every probe, and
rerun the suite themselves. They can dispute results; they cannot dispute the
method — and contestable results are the point of a measurement, not a flaw.