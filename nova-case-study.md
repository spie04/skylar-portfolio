# Nova: Building a Research System That Can Say “I Don't Know”

## Overview

Nova began as an independent effort to build a quantitative trading research platform. The hard problem soon stopped being how to produce a signal. It became how to determine whether the system had earned the right to believe one.

That led to the project's central constraint: **nothing should claim more than its evidence supports.** Missing information must not become zero, partial success must not become complete success, synthetic input must not become genuine evidence, and research output must not become executable action without validation and operator authority.

## My role and the role of AI

Nova was developed through a substantially AI-assisted process. I do not present its source as tens of thousands of lines typed manually without assistance.

My demonstrated ownership centered on defining the problem and architecture; decomposing responsibilities; specifying contracts, invariants, and acceptance criteria; reviewing and challenging generated implementation; tracing inconsistencies across components; making integration decisions; debugging failures; expanding tests; and requiring concrete evidence before classifying a capability as proven.

This process taught me that faster code generation increases the need for architectural discipline. It becomes easier to create duplicated responsibility, hidden dependencies, plausible-looking failure, and source code that exists without being genuinely connected.

## Architecture: a governed truth funnel

Nova uses a seven-stage unidirectional pipeline:

```text
ingest_raw → clean_raw → enrich → features → regime → story → cards
```

Every transition requires a governed handoff. The system's ten coded axioms include one legal path, evidence before promotion, no forbidden jumps, and human authority over execution. The broader system covers contracts and provenance, governance, API services, order and position management, risk, backtesting, experiment controls, providers, audit tooling, observability, and scheduled operations.

The design principle is that a component may perform its responsibility without accumulating authority that belongs elsewhere.

## Auditing capability instead of assuming it

I used a five-rung evidence ladder:

```text
implemented → connected → exercised → externally proven → economically validated
```

Each rung requires a different form of evidence. Source code can show that something is implemented, but a downstream database row, genuine input, external confirmation, or economic result may be required for a stronger claim. Anything unprovable is classified down, never up.

This method exposed a hardcoded health score, conflicting regime classifiers, synthetic volatility without provenance, a post-fill error that still looked successful, and broker protection implied by the architecture but absent from the connected path.

## Four failures that looked plausible

**Vanishing history.** Thirty days of Interactive Brokers history were fetched for ATR computation and then discarded. The fix persisted full history with provenance.

**False zero values.** A swallowed exception stamped a failed return calculation as `0.0`. The fix recorded `NULL` and logged the failure so missing and genuinely flat data remained different.

**Time-zone mismatch.** UTC bars were compared against an Eastern-time regular-session filter, allowing a “09:30” scan to use pre-market, zero-volume bars. The result looked reasonable but was invalid.

**Disconnected configuration.** Environment values did not load through every execution path, causing wrong-port broker connections and live-venue settings to leak into tests.

These reinforced a debugging rule: find the first boundary where correct information becomes incorrect rather than rewriting the component nearest the symptom.

## End-to-end proof

One genuine market observation was traced through 14 unbroken links using real IBKR data and paper execution: ingestion, durable storage, features, regime classification, candidate discovery, playbook applicability, risk qualification, operator approval, paper order, held position, rule-driven exit, linked outcome, learning signal, and evidence consumed by a later decision.

The execution path was separately audited across submit, capture, recognize, close, exit, reconcile, and fail-loud boundaries. Known limits—such as software-only rather than broker-native protection—were recorded explicitly.

## Experiment discipline

Experiment contracts freeze criteria before a run. They declare hypothesis counts, raise significance requirements when many variants are tested, enforce sample and concentration limits, and use three-way verdicts: edge, no edge, or insufficient evidence. A no-edge finding retires that version; it is not repeatedly retuned against the same data.

The system rejected or withheld judgment when evidence did not support a stronger conclusion. A daily-bar result was prevented from being inherited by an intraday implementation after a parity run showed the latter produced 15 times fewer signals.

## Safety and testing

Risk limits fail closed when unconfigured. Every execution requires explicit human approval and a matching payload hash. Ambiguous bars touching both target and stop resolve conservatively as the stop. Direction is encoded as a constrained type, and provenance travels with data.

The audited suite grew from roughly 2,000 to roughly 2,900 passing tests. Integration tests were added when unit tests passed but multi-stage runtime behavior failed. Large clusters of failures were traced to shared causes rather than patched assertion by assertion.

## Outcome

The audited system contained approximately 29,000–35,000 lines of Python across approximately 200 modules, 25 database tables, and 156 REST routes. All 240 recorded orders were on a paper venue. No real capital was deployed and no strategy was cleared for it.

Nova has not demonstrated a trading edge. Its strongest demonstrated property is that it can produce trustworthy negative or insufficient findings—and preserve them as such. That honesty is what would make any eventual positive result worth investigating.
