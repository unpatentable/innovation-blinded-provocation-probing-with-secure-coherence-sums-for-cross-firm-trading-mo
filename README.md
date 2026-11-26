# Blinded Provocation Probing with Secure Coherence Sums for Cross-Firm Trading Model Crowding

> **Note:** This README is an ultra-condensed summary of the research reports published by Unpatentable.org. To view the full innovation documentation and explore the complete research, use the links at the bottom of this document.

**Domain:** [Social & Organizational Systems](https://unpatentable.org/innovation/?domain=social-organizational-systems)
**Subdomain:** [Algorithmic Trading](https://unpatentable.org/innovation/?domain=social-organizational-systems&subdomain=algorithmic-trading)

---

### Why We Invented This Technology
Retail and retirement savers increasingly have their orders, rebalancing and now AI-agent trades executed by a small number of similar machine-learning models trained on overlapping data; as these strategies converge, they will sell the same assets at the same moment, turning ordinary shocks into liquidity collapses within seconds. Those who suffer are the roughly 100+ million households in defined-contribution pensions and index funds (plus retail traders using copy-trading and LLM agents), who are forced sellers or stop-loss victims at the bottom: the 2010 flash crash temporarily erased about $1 trillion in minutes, and a correlated-model event in a far more automated market could lock in permanent losses of several percent of savings for people near retirement. No one currently measures cross-firm strategy similarity, because each firm's models are secret, so regulators and firms cannot see the crowding build; privacy-preserving computation now makes that measurable without revealing proprietary strategies. This sizing is my own estimate from general knowledge, not checked against a fresh search.

### The Innovation
## Abstract
This instrument measures how similarly independent firms' trading models would react to stress, without any party seeing a model, a position or an individual response. A hub broadcasts counterfactual market states ("provocations", including synthetic headlines for LLM agents) to shadow forks of production models. Firms return paired response vectors through secure aggregation. Three secure sums give the exact size-weighted mean pairwise response cosine across all firm pairs. Iterating aggregate flow through a public impact function gives a counterfactual market reproduction number. An evolutionary search, scored only on those aggregates, finds the plausible scenario in which the collective is most self-reinforcing. Each firm privately learns its own crowd alignment, as in decentralized exposure notification.

## Background & Prior Work
Crowding is inferred today from lagged holdings (13F overlap, commercial crowding scores) or from realized trades: Hawkes branching ratios (Filimonov & Sornette 2012) and mean pairwise correlation of realized agent actions (MDPI FinTech, found by search). These see positions or past behaviour. They do not see how models would react in states not yet visited, which is where convergent models synchronize.

Secure computation has been applied to exposures (Abbe, Khandani & Lo 2012), per-symbol long/short totals (ACM 2022) and portfolio overlap (IACR ePrint 2026/2879). The Macroprudential AI Stress Test proposal (arXiv 2604.03272) describes shared-signal shocks, pairwise model-similarity checks and second-round scenarios; the search summary showed no privacy-preserving collection mechanism in it. The Bank of England's system-wide exploratory scenario ran its rounds manually over months.

This concept differs in three ways. It measures response functions, not holdings. It obtains all-pairs similarity without any pairwise comparison. It searches adversarially for the collective's worst plausible scenario under secure aggregation.

## Competitive Landscape & Strategic Edge
Data vendors and prime brokers sell positional crowding scores. Regulators hold order audit trails, which are post-trade. Exchanges run static circuit breakers. Privacy-technology firms offer multiparty computation for exposure pooling.

The first blind spot is that all of these treat crowding as overlap in what firms hold. ML and LLM agents with different books can share a reaction function inherited from common foundation models, data vendors and prompts. The second blind spot is the assumption that someone must inspect models or pairwise outputs to measure similarity.

The edge here is an algebraic identity that makes pairwise inspection unnecessary. Participation also pays the firm directly, because each firm privately receives its own crowding exposure, which positional vendors cannot supply for counterfactual states.

## Core Concept
**Architecture.** The system uses four existing components.
- **Probe hub:** run by an exchange consortium, a clearing house or a supervisor.
- **Shadow-fork harness:** sits at each firm's pre-trade risk gateway, the control point already required by SEC Rule 15c3-5. It snapshots the production model's full state copy-on-write (weights, positions, limits, agent memory), replays a modified market-data and news stream to the fork, and routes the fork's orders to a null sink.
- **Secure-aggregation client:** follows Bonawitz et al. 2017 with threshold t = ⌈2N/3⌉.
- **Commitment log:** append-only.

**Paired provocation.** Each cycle the hub broadcasts P probes. A probe is the live state s₀ plus a perturbation δ made of:
- returns for K = 64 public risk buckets (equity sector-size, rates tenors, credit, FX, commodity, volatility);
- depth multipliers of 0.2–1 and volatility shifts;
- optionally, hub-signed synthetic headlines drawn from a template library.

The fork runs twice with common random seeds, once on the baseline and once provoked. The response rᵢ(s) ∈ ℝ³ᴷ is the paired difference in intended net signed delta-notional per bucket over 1 s, 1 min and 30 min horizons, quantized to $10k units. Stochastic LLM agents average 4–8 seeded replicates. Like a crossover trial, the pairing cancels scheduled and idiosyncratic trading and isolates the reaction. Vectors are pre-scaled by public impact weights so that norms are in price-impact units.

**Three-sum coherence identity.** Under secure aggregation each firm submits rᵢ, wᵢ = ‖rᵢ‖ and qᵢ = ‖rᵢ‖². The hub learns only F = Σrᵢ, S₁ = Σwᵢ and S₂ = Σqᵢ. Since Σᵢ≠ⱼ⟨rᵢ,rⱼ⟩ = ‖F‖² − S₂ and Σᵢ≠ⱼwᵢwⱼ = S₁² − S₂, the size-weighted mean pairwise cosine over all firm pairs is exactly

ρ̄(s) = (‖F‖² − S₂) / (S₁² − S₂).

ρ̄ is near 0 for unrelated models and 1 for identical reaction functions. The effective number of independent strategies is N_eff = S₁²/‖F‖². No pair is ever compared. Release rules are N ≥ 20 contributors, suppression if any firm exceeds 25% of S₁, and publication of ρ̄ in 0.05 bins.

**Counterfactual reproduction number.** A public impact function I(·) maps aggregate flow to price displacement. One choice is the empirical square-root law ΔPₖ/Pₖ = Y·σₖ·sign(Qₖ)·√(|Qₖ|/Vₖ), with Y of order 0.5–1 and σₖ, Vₖ the daily volatility and volume of bucket k. The hub iterates the fixed point D⁽⁰⁾ = δ, D⁽ʲ⁺¹⁾ = δ + I(F(s₀ ⊕ D⁽ʲ⁾)) for up to five rounds, each round being a new probe. The amplification is A = ‖D*‖/‖δ‖ and the market reproduction number is ℛ = 1 − 1/A, so a geometric cascade satisfies A = 1/(1−ℛ). Non-convergence, or D exceeding the 7% market-wide circuit-breaker level, is flagged as ℛ ≥ 1.

A sentinel panel of P = 16 probes (12 standard, 4 randomized each cycle to prevent tuning) gives a continuous series. As a timing estimate, 16 probes × 4 replicates is 64 parallel evaluations per round. At roughly 10 s per LLM-agent evaluation, five rounds take about one minute. Aggregating 192-element vectors for up to 200 firms should add seconds.

**Collective resonance search.** CMA-ES (Hansen & Ostermeier 2001) searches a d = 40 space: 32 principal components of bucket returns and volatility, plus 8 headline-topic intensities. The constraint is the plausibility ball δᵀΣ⁻¹δ ≤ r² with r = 3–6 (Breuer et al. 2009). Population is λ = 4 + ⌊3 ln d⌋ = 15, run for 20–40 generations and warm-started from the previous optimum. Fitness is ℛ(δ). The search is gradient-free, so it works on non-differentiable LLM agents, and it consumes only aggregates. It outputs the resonant scenario δ* and ℛ_max. It runs every 15–30 minutes for sub-second models and nightly for LLM agents.

**Integrity and private feedback.** Each firm posts Pedersen commitments to rᵢ. The homomorphic product of commitments must open to F, which proves the aggregate equals the sum of committed values. The supervisor opens a random 1–2% of commitments per firm per day. It compares them with the firm's realized audit-trail flow during "natural probe" windows, when the realized state came within Mahalanobis distance 0.5 of a past probe. Persistent mismatch flags an unfaithful fork.

For exposure notification, the hub sends Paillier-encrypted F and the firm returns Enc(⟨rᵢ,F⟩ + μ) with a random mask μ. The hub decrypts and returns the masked value, and the firm unmasks it. The firm then computes locally aᵢ = (⟨rᵢ,F⟩ − qᵢ) / (wᵢ·√(‖F‖² − 2⟨rᵢ,F⟩ + qᵢ)), its cosine with everyone else. Neither the hub nor peers learn aᵢ, mirroring on-device matching in DP-3T contact tracing. Binned ρ̄ and ℛ are public. δ* is restricted to supervisors and venue risk officers.

## Physics & Logical Feasibility
The coherence identity is exact algebra. Secure aggregation, Pedersen commitments, Paillier encryption, copy-on-write snapshots and CMA-ES are deployed technologies. The fixed-point iteration converges when the composed impact-response map is a contraction, and non-convergence is itself the supercritical signal. "Real time" here means a minute-scale cadence set by the slowest participating model, not microseconds.

Critical assumptions:
- Fork fidelity, enforced by commitment audits against realized flow.
- Participants cover a large share of order flow.
- The impact function is approximate, so ℛ is a conditional estimate, not a forecast.

One limit is stated openly. Vector-level Gaussian differential privacy at ε = 1, δ = 10⁻⁶ needs σ ≈ 5.3c per coordinate for clip norm c. For 32 dimensions that is a noise norm of about 30c against a maximum coherent signal of N·c. It is therefore an optional embodiment for N ≳ 100. The primary protections are aggregation, cohort and dominance rules, and binning.

## Implementation Readiness & Key Constraints
- **Phase 1 (months 0–18):** build the open-source harness, bucket taxonomy and protocol. Validate in agent-based simulators such as ABIDES with open LLM trading agents and planted model similarity, to test recovery of known ρ̄ and ℛ.
- **Phase 2 (months 18–36):** run a regulatory-sandbox pilot with 20–30 firms on the sentinel panel only, with an independent cryptographic audit.
- **Phase 3 (years 3–5):** add resonance search, audit-trail integration and venue hooks.

Estimated total cost is $30–45M, under the $50M ceiling. This is an estimate covering engineering, security review and pilot operations. Firms bear the shadow inference compute.

Key constraints:
- Supervisory mandate or incentive to participate.
- Snapshot fidelity for stateful LLM agents that call tools.
- Inference cost of replicates.
- Governance of probe libraries and δ* access.

## Utility & Purpose
The instrument makes latent reaction-function convergence observable before a trigger event, with no disclosure of models or positions. Supervisors gain a continuous fragility gauge and a concrete worst plausible scenario. Venues and clearing houses can pre-arm volatility auctions, price bands and margin add-ons when ℛ rises. Firms learn privately when their strategy sits inside a crowd and can diversify voluntarily. The ultimate beneficiaries are pension and retail savers, whose forced selling at cascade lows becomes less likely. The thresholds for these actions must be calibrated empirically and are not asserted here.

## Potential Applications & Impact
- **Macroprudential dashboards:** ρ̄ and ℛ by asset class.
- **Circuit-breaker calibration:** tied to measured loop gain in place of fixed percentages.
- **Margin models:** clearing-house models that price crowd-alignment.
- **Model-risk management:** aᵢ as a diversification metric.
- **Vendor due diligence:** detecting when a newly adopted foundation model raises a firm's alignment.

The three-sum identity and the aggregate-only adversarial search also generalize to other settings where homogeneous autonomous agents raise correlated-failure concerns, such as credit underwriting models, insurance pricing engines and power-market bidding agents. In the longer term, routine publication of an effective strategy count could make model diversity a measured market property, much as capital ratios made leverage one. It would also give empirical grounding to the algorithmic-monoculture literature.

## Market/Adoption Feasibility (If Applicable)
The harness attaches to gateways and market-data replay infrastructure that firms already operate for backtesting and pre-trade risk checks. Per-firm integration is therefore mainly software plus inference capacity. Adoption levers are the private exposure score, supervisory sandbox credit, and possible margin relief for low-alignment participants. Open-source publication removes licensing barriers and allows multiple hubs across jurisdictions.

Barriers:
- Legal comfort that submitting masked responses is not disclosure of trade secrets.
- Coverage thresholds, since the release rules require at least 20 contributors.
- Agreement on bucket taxonomy and impact parameters among venues and supervisors.

## Risk & Counterarguments
- **Weaponization:** δ* could guide a manipulator, so access is restricted and aggregates are never published as vectors.
- **Gaming:** firms may run sanitized forks. Random commitment openings checked against realized flow deter this but cannot eliminate it.
- **Homogenization:** a search-found paper (arXiv 2607.15385) warns that common supervisory scenarios can increase homogeneity. Here the probes are randomized, unscored at firm level and never used as training targets, but the concern remains.
- **Collusion:** a hub colluding with more than N − t firms defeats aggregation privacy.
- **Front-running:** aggregate flow has front-running value.
- **False reassurance:** a low ℛ under probed scenarios does not cover unprobed ones.
- **Antitrust:** competition counsel must confirm that aggregate-only exchange is permissible.

## Open Questions & Future Research Directions
- How well do paired shadow responses predict realized stress flow, and at which horizons?
- What probe-panel size and dimension give stable ρ̄ estimates for stochastic LLM agents?
- Can formal differential privacy be achieved at practical cohort sizes through dimension reduction or shuffling?
- How should cross-impact and funding constraints enter I(·)?
- Can trusted execution attest that the fork and production share weights?
- Does publishing ℛ change behaviour and so the measurement itself?
- Can headline provocations be standardized across languages and modalities?
- Which threshold policies reduce cascades without impairing liquidity?
- How can hubs in different jurisdictions be federated?

## Novelty Threshold Justification
**Medium Novelty.** The closest prior art is the MAST proposal ([arXiv 2604.03272](https://arxiv.org/pdf/2604.03272)), which per the search summary already combines shocks, pairwise similarity checks and second-round feedback. Next closest are secure aggregation of positions ([ACM 2022](https://dl.acm.org/doi/fullHtml/10.1145/3533271.3561776); [Abbe et al.](https://arxiv.org/abs/1111.5228)) and privacy-preserving portfolio overlap ([IACR 2026/2879](https://eprint.iacr.org/2026/2879)). Probing, cascade rounds and secure sums are therefore not claimed individually. The new elements are:
1. Paired shadow-fork responses to counterfactual probes as the aggregated quantity.
2. The three-sum identity, which gives exact all-pairs response similarity with no pairwise disclosure.
3. Evolutionary resonant-scenario search scored only on secure aggregates.
4. A blinded inner-product exposure notification.

None of the three searches surfaced this combination. Three searches cannot establish absence.

## References & Prior Art Checks
From recall:
- Bonawitz et al., "Practical Secure Aggregation," ACM CCS 2017.
- Breuer, Jandačka, Rheinberger & Summer, IJCB 2009.
- Filimonov & Sornette, Phys. Rev. E 2012.
- Hansen & Ostermeier, Evolutionary Computation 2001.
- Troncoso et al., DP-3T, 2020.
- Tóth et al., Phys. Rev. X 2011.
- Byrd, Hybinette & Balch, ABIDES, 2019.
- Bank of England system-wide exploratory scenario, 2024.
- SEC Rule 15c3-5.

Found by search:
- [arXiv 1111.5228](https://arxiv.org/abs/1111.5228)
- [ACM 3533271.3561776](https://dl.acm.org/doi/fullHtml/10.1145/3533271.3561776)
- [IACR 2026/2879](https://eprint.iacr.org/2026/2879)
- [arXiv 2604.03272](https://arxiv.org/pdf/2604.03272)
- [arXiv 2607.15385](https://arxiv.org/pdf/2607.15385)
- [arXiv 2604.22818](https://arxiv.org/abs/2604.22818)
- [MDPI FinTech](https://doi.org/10.3390/fintech5020034)
- [arXiv 2610.08806](https://arxiv.org/html/2610.08806v1)

**Prior art searched:**
1. "privacy-preserving secure multiparty computation detect crowded trades strategy crowding across hedge funds without revealing positions". Used: Abbe/Khandani/Lo (arXiv 1111.5228), ACM 2022 secure aggregation, IACR ePrint 2026/2879.
2. "system-wide stress test trading algorithms common counterfactual scenario probes aggregate model responses secure aggregation AI herding monoculture supervisory". Used: arXiv 2604.03272 (MAST), arXiv 2607.15385, arXiv 2610.08806.
3. "measuring behavioral similarity of AI trading agents across firms shadow model probing synthetic market shocks correlated responses financial stability patent OR paper". Used: arXiv 2604.22818, MDPI FinTech doi 10.3390/fintech5020034. No patents surfaced.

Search-found papers are characterized from result summaries only; their full texts were not read.

---

### About the Reports
Each innovation on Unpatentable.org is published as a three-part open documentation set:
- **Innovation Report** – the master blueprint describing the first principals concept, mechanism, and rationale behind the invention.
- **Implementation Guide** – a step-by-step, practical manual detailing how to build or prototype the innovation. Includes estimated cost breakdown.
- **Industry Impact Report** – an analytical overview exploring the potential economic, industrial, and societal effects of the innovation.

Together, these documents form a complete public disclosure package — enabling researchers, developers, and policymakers to understand, replicate, and build upon each invention.

---

### Explore this Innovation
- [Innovation Report](https://unpatentable.org/innovation/blinded-provocation-probing-with-secure-coherence-sums-for-cross-firm-trading-model-crowding)
- [Implementation Guide](https://unpatentable.org/innovation/implementation-guide-blinded-provocation-probing-with-secure-coherence-sums-for-cross-firm-trading-model-crowding)
- [Industry Impact Report](https://unpatentable.org/innovation/industry-impact-report-blinded-provocation-probing-with-secure-coherence-sums-for-cross-firm-trading-model-crowding)

### Download PDFs
- [Innovation Report (PDF)](https://unpatentable.org/wp-content/uploads/2026/11/Blinded-Provocation-Probing-with-Secure-Coherence-Sums-for-Cross-Firm-Trading-Model-Crowding.pdf)
- [Implementation Guide (PDF)](https://unpatentable.org/wp-content/uploads/2026/11/Implementation-Guide-Blinded-Provocation-Probing-with-Secure-Coherence-Sums-for-Cross-Firm-.pdf)
- [Industry Impact Report (PDF)](https://unpatentable.org/wp-content/uploads/2026/11/Industry-Impact-Report-Blinded-Provocation-Probing-with-Secure-Coherence-Sums-for-Cross-Fir.pdf)

---

**Timestamps:**
- [View Main Innovation Report on Arweave](https://arweave.net/sRFGxnCMq9vw7sTViRv1Ktzj8hylvqTpAlLg3rTAVg8)
- [View Implementation Guide on Arweave](https://arweave.net/1jEblbL2zhtJDCDInVOP4SKk6GHzHP18nYHUefpg-xE)

**Suggested Citation:**
> Unpatentable.org (2026). *Blinded Provocation Probing with Secure Coherence Sums for Cross-Firm Trading Model Crowding.* Defensive Publication Series.
> https://unpatentable.org/innovation/blinded-provocation-probing-with-secure-coherence-sums-for-cross-firm-trading-model-crowding

---

```json
{
  "domain": "Social & Organizational Systems",
  "subdomain": "Algorithmic Trading",
  "published": "2026-11-26",
  "arweave_main_tx": "sRFGxnCMq9vw7sTViRv1Ktzj8hylvqTpAlLg3rTAVg8",
  "license": "CC BY 4.0",
  "arweave_implementation_tx": "1jEblbL2zhtJDCDInVOP4SKk6GHzHP18nYHUefpg-xE",
  "value_unlocked": "$118B"
}
```

Unpatentable.org is a public library of timestamped defensive publications —
open innovations released into the commons to prevent monopolization, democratize knowledge, and accelerate human progress.

License: CC BY 4.0
