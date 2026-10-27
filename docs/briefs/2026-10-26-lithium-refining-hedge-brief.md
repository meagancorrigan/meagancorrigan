---
type: brief
engagement: fx-hedging
capability: fx-hedging
date: 2026-10-27
status: committed
hypothesis: "I expect the forward and money-market hedge to lock in within about 0.5 cent of each other, and the put's all-in floor to sit below the forward rate, because the first two share one interest-rate gap while the put charges 2.24 cents per euro for flexibility."
---

# Lithium Refining: EUR Receivable Hedge Brief

**To:** CFO
**From:** Meagan
**Date:** October 27, 2026

## The exposure

We have invoiced a German battery maker EUR 11,000,000, payable in 273 days. The euro amount is fixed. The dollar amount is not. Locking today at the indicative forward of 1.1756 would give us $12,931,600. If the euro falls 5% before settlement, we collect about $647,000 less. Every one-cent move in EURUSD is worth $110,000 to us. Only a weaker euro hurts.

## Three ways to hedge

| Tool | How it works | Upside | Downside |
|---|---|---|---|
| Forward | Sell EUR 11M at 1.1756 in 273 days | Certainty, so the budget is safe | We give up the gain if the euro strengthens; counterparty and margin terms apply |
| Money-market hedge | Borrow euros today, convert at spot, invest the dollars until settlement | Same certainty as a forward, and useful if forward quotes look off | Uses borrowing capacity, more steps |
| Put option | Pay 0.0224 per euro ($246,400) for the right to sell EUR at today's spot | A floor under proceeds with the upside kept | The premium is gone whether we need the floor or not |

A forward is like selling the house at a fixed price before the market moves. A put is fire insurance: we pay a premium and hope to waste it. The call quoted in our scenario (0.0376) protects a euro payer, not a receiver, so it is not a tool for this exposure.

## What I am assuming

- The EUR 11M is certain in amount and date, so we hedge price, not volume.
- Spot and the USD and EUR interest rates are placeholders until Stage 4, and the forward and option quotes are indicative.
- Credit, margin and the cost of financing the premium are small next to the rate and premium effects. I would test this with more time.

## Hypothesis

I expect the forward and the money-market hedge to lock in within about 0.5 cent of each other, roughly $55,000 on this size, because both come from the same gap between dollar and euro interest rates. I expect the put's all-in floor, the strike minus 2.24 cents, to sit below the forward rate. The put would then beat the forward only if the euro finishes more than a couple of cents above the forward. That would make the put the costly choice unless we want the upside.

## How I would know I was wrong

If sourced rates show the forward and money-market hedge more than $55,000 apart, or the put's floor above 1.1756, the hypothesis fails and the mechanism needs rethinking.

## Next steps

This brief comes before any model. The work that follows:

- **Stage 2, specification:** design the workbook on paper, including named ranges, tabs, formulas and checks.
- **Stage 3, build and audit:** have an AI generate the workbook from the spec, then audit and fix it.
- **Stage 4, market data:** replace placeholders with sourced, timestamped spot and interest rates.
- **Stage 5, validation and decision memo:** run an independent check, verify by hand, and make the recommendation.

No hedge is recommended until Stage 5.
