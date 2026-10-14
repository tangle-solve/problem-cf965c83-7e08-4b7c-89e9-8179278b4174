<!-- solve-problem:cf965c83-7e08-4b7c-89e9-8179278b4174 -->
# Incentive-compatible liquidation penalties

A [Tangle Solve problem](https://solve.tangle.tools/problems/cf965c83-7e08-4b7c-89e9-8179278b4174).

## What is the problem?

In automated lending protocols (e.g., Aave, Compound, MakerDAO), collateralized loans are liquidated when collateral value falls below a threshold. The protocol designer sets two parameters: a **liquidation bonus** (a reward, typically 5–15% of repaid debt, paid from the borrower's collateral) and a **close factor** (a cap on the fraction of a position a single liquidator may close per transaction). Liquidation is executed by external agents ("liquidators") who must invest capital, monitor prices, bid gas/priority fees, and compete (often in MEV auctions or gas races).

**Formalize as a mechanism design problem.** There is a continuum (or finite set) of borrowers whose positions become undercollateralized at stochastic times; collateral and debt prices follow some stochastic process (e.g., geometric Brownian motion). Liquidators choose monitoring intensity, capital allocation, and transaction expenditure; competition among them determines realized liquidation latency and gas cost. Social objective: minimize **expected bad debt** (uncollateralized loss borne by lenders/insurance pools) net of total liquidation costs and excess borrower losses, i.e., some weighted objective W(bonus b, close factor f, and any richer mechanism) over equilibrium outcomes.

**Open question:** characterize (analytically or via calibrated simulation) the (b\*, f\*) — or a better mechanism, e.g., auctions, Dutch-auction liquidations, penalty escrows — that is **incentive-compatible**: liquidators' equilibrium strategies execute liquidations fast enough to bound bad debt, without the bonus overcompensating liquidators and expropriating borrowers. Ambiguity note: the census does not fix the objective's precise form (borrower-loss weight, risk aversion, latency cost); you must state and justify one.

## Why does it matter?

Liquidation parameters directly trade off lender solvency against borrower wealth extraction: Perez et al. showed liquidators extracted ~$800M in bonuses (2020–2021), often far exceeding gas costs, while other empirical work shows gas-fee races can *destroy* most of that value. A principled theory would let protocols replace hand-tuned bonuses with near-optimal mechanisms, reduce bad-debt accrual during crashes (e.g., March 2020, May 2021), and inform designs like Maker's Dutch auctions. It is also a clean instance of mechanism design with costly monitoring — results may transfer beyond blockchain.

## What's already known?

- **Empirics:** Perez, Werner, Xu, Livshits (arXiv:2009.13235) document gas races, partial liquidations, and large liquidator profits on Compound/Aave/Maker; they note bonuses frequently exceed liquidation costs by orders of magnitude on popular assets. Qin, Zhou, Gervais et al. (2021) (verify exact citation) quantify liquidation-related MEV. These papers measure but do not optimize.
- **Theory:** Existing protocol theory treats liquidation as exogenous or models liquidators as risk-neutral with exogenous latency; no full equilibrium model with endogenous monitoring/capital investment and strategic competition exists for the bonus–close-factor pair, to our knowledge. Related literature: bankruptcy auction design (e.g., work on descending auctions for distressed assets) and costly-state-verification/monitoring models in contract theory — neither maps directly.
- **Practice:** Protocols have moved to Dutch-auction liquidations (Maker, Liquity) with essentially no formal optimality justification; the choice remains heuristic.

## What would count as solving it?

1. **Model + theorem:** a tractable model with endogenous liquidator entry, monitoring, and competition, and a proof of the optimal (b\*, f\*) — e.g., "expected bad debt is bounded by ε iff b ≥ b(ε), and borrower loss is minimized at b = b(ε)"; or a proof that no fixed (b, f) achieves a target bound and an auction-like mechanism does (impossibility + construction).
2. **Matching bound:** incentive-compatibility lower bound on the bonus (liquidators must cover monitoring + gas costs in expectation) paired with an upper bound showing excessive bonuses only transfer wealth from borrowers.
3. **Calibrated simulation** reproducing empirical regularities (gas races, partial liquidations, bonus-to-cost ratios from Perez et al.) and demonstrating the proposed mechanism reduces a stated loss metric — acceptable as a solution to the applied variant.

## How to start

1. Read Perez et al. (2021), Sections on gas races and profitability, to fix stylized facts your model must reproduce (bonus-to-cost ratios, latency, competition structure).
2. Build a two-period toy model: one borrower, one liquidation opportunity, Poisson arrival, n liquidators choosing monitoring cost; derive bonus needed for participation and the resulting race dissipation. Main obstacle: latency-dependent bad debt makes the objective discontinuous in equilibrium entry.
3. Extend to close factor as a tool for time-sharing liquidation across liquidators; note the empirical puzzle that large positions are often left partially unliquidated.
4. Compare against a Dutch-auction benchmark (Liquity) to test whether the optimal static mechanism is dominated.

## Key references

- Perez, D., Werner, S. M., Xu, J., Livshits, A. (2021). "Liquidations: DeFi on a Knife-edge." arXiv:2009.13235. Empirical study of liquidations on Compound, Aave, and Maker showing gas-fee competition, profitability, and extracted bonuses. https://arxiv.org/abs/2009.13235
- (Verify) Qin, K., Zhou, L., et al. on quantifying liquidation MEV, ~2021 — measures liquidators' extractable value in busy periods.
- Tirole/Laffont-style costly monitoring contract theory, and bankruptcy-auction literature — background for mechanism design with costly state verification (no single canonical blockchain-specific citation; survey yourself).

## Warnings

- The census statement is informal: "incentive-compatible" is not precisely defined for this setting — you must choose and defend a solution concept (e.g., symmetric equilibrium in monitoring/bidding games).
- Empirical gas-race data is chain- and period-dependent; calibrating to 2020–2021 (pre-EIP-1559, pre-POS) may misrepresent current priority-fee competition.
- Don't model liquidators as a single price-taking agent — competition structure is the crux; ignoring it collapses the problem to trivial "bonus = cost."
- Beware double-counting: MEV-searcher rents vs. validator capture means observed liquidator profits understate gross bonuses.

---
*`BCGT-000340` · flagged for review*

## Acceptance guidance

1. **Model + theorem:** a tractable model with endogenous liquidator entry, monitoring, and competition, and a proof of the optimal (b\*, f\*) — e.g., "expected bad debt is bounded by ε iff b ≥ b(ε), and borrower loss is minimized at b = b(ε)"; or a proof that no fixed (b, f) achieves a target bound and an auction-like mechanism does (impossibility + construction).
2. **Matching bound:** incentive-compatibility lower bound on the bonus (liquidators must cover monitoring + gas costs in expectation) paired with an upper bound showing excessive bonuses only transfer wealth from borrowers.
3. **Calibrated simulation** reproducing empirical regularities (gas races, partial liquidations, bonus-to-cost ratios from Perez et al.) and demonstrating the proposed mechanism reduces a stated loss metric — acceptable as a solution to the applied variant.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/cf965c83-7e08-4b7c-89e9-8179278b4174?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000340.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000340)
