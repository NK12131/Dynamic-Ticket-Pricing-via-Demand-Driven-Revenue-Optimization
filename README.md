# Airline Ticket Pricing Optimization

Maximizing revenue from a fixed ticket inventory across a multi-day selling window using dynamic pricing from heuristic baselines through dynamic programming.

---

## Problem

You have a fixed number of tickets to sell before an event. Each day you set a price, and daily demand drawn from a uniform distribution between 100 and 200 determines how many tickets sell. Unsold tickets at event time are lost.

**The core tension:** sell aggressively now at a lower price, or hold inventory for potentially higher demand later?

Key mechanics:
- `tickets_sold = demand price` on any given day
- Demand is uniform U(100, 200) unknown until the day of pricing
- Unsold tickets on the final day are permanently lost
- Objective: maximize total revenue across all days

---

## Mathematical Foundation

Revenue as a function of price:

```
Revenue = price × (demand − price)
```

Taking the derivative and setting to zero:

```
dRevenue/dprice = demand − 2 × price = 0
→ optimal price p* = demand / 2
→ optimal tickets sold = demand / 2
```

This optimal price only holds when remaining inventory ≥ demand / 2. When inventory is low, the floor price is `demand tickets_remaining`.

**Key insight from uniform demand:**
- P(demand > D) = (200 − D) / 101
- E(demand) = 150
- P(demand > 150) ≈ 50%

---

## Core Strategy

The naive approach — sell as many tickets as possible when demand is high — is suboptimal. Consider 30 tickets remaining with demand at 180:

| Strategy | Day 1 | Day 2 | Total |
|:---|:---|:---|:---|
| Sell all now | 30 tickets @ 150 → **€4,500** | — | €4,500 |
| Split across days | 15 tickets @ 165 → €2,475 | 15 tickets @ 135+ → €2,025+ | **€4,500+** |

Splitting creates upside: if day 2 demand exceeds 150 (50% probability), revenue beats the all-in strategy. The optimization opportunity is **selling smaller quantities at higher prices and repeating** capturing demand variance rather than surrendering to it.

---

## Approaches & Results

### Baseline Heuristic Pricing Rules
Segment-based pricing logic using demand thresholds and remaining inventory. Progressively refined segmentation improved results.

**Average revenue: €7,348**

### Brute Force Precomputation
Precomputes optimal prices across every combination of `(days_remaining, tickets_remaining, demand_level)` before the selling window opens. At runtime, looks up the precomputed optimal price rather than calculating on the fly.

**Average revenue: €7,574** (+3% over heuristic baseline)

### Dynamic Programming
Solves the optimization recursively from the final day backward. Starts with the trivially solvable 1-day problem, then expands one day at a time each solution informing the next. Avoids redundant computation by storing subproblem solutions.

**Average revenue: €7,596**

### Next Step  Bellman Equation
The Bellman equation decomposes the value function into immediate reward plus discounted future value, solving the full optimization without brute-force enumeration. Achieves **€7,611** on the test dataset and connects directly to Reinforcement Learning this pricing problem is structurally equivalent to a finite-horizon MDP.

---

## Results Summary

| Approach | Avg Revenue | vs. Baseline |
|:---|:---:|:---:|
| Heuristic Rules | €7,348 | — |
| Brute Force | €7,574 | +3.1% |
| Dynamic Programming | €7,596 | +3.4% |
| Bellman Equation | €7,611 | +3.6% |

---

## References

- [Kaggle — Airline Price Optimization Micro-Challenge](https://www.kaggle.com/alexisbcook/airline-price-optimization-microchallenge)
- [Refined DP Solution](https://www.kaggle.com/aliaksei0/airline-price-optimization-micro-challenge)
- [Bellman Equation Implementation](https://www.kaggle.com/alexishchenko/airline-price-optimization-micro-challenge)