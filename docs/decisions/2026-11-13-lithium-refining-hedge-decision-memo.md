# Hedge Decision Memo: EUR 11,000,000 Receivable

**To:** CFO
**From:** Meagan
**Date:** November 13, 2026

## A. Exposure

We are owed EUR 11,000,000 by our German battery customer in 273 days. At today's spot of 1.1081 that is worth USD 12,189,100. If the euro falls 5% before payment arrives, we collect about USD 609,000 less. Each 1 cent move is USD 110,000. The amount and the date are certain, so the only open question is the exchange rate.

## B. What each strategy delivers

| Strategy | USD received | What it means |
|---|---|---|
| Forward at 1.1756 | 12,931,600, fixed | The best result in every case I tested, and we know it today. |
| Money-market hedge | 12,313,405, fixed | Same certainty, but USD 618,195 less than the forward. It is the price we can build ourselves. |
| Put at 1.1081 | At least 11,942,700 | Protects the downside, costs USD 246,400 up front, and only pays off if the euro rises. |
| Short call | At most 12,602,700 | Brings in USD 413,600 but leaves us exposed on the downside, which is the risk we want to remove. |
| Unhedged | 11,579,645 to 12,798,555 | The whole range of outcomes is ours to carry. |

The long call is not on the table. It pays when the euro rises, which already helps us.

## C. What happens if the euro moves

If the euro weakens, the forward and the money-market hedge don't care. The put floors us at USD 11.94 million, which is USD 989,000 below the forward. The short call and the unhedged position both fall with the euro.

If the euro strengthens, the forward still pays USD 12,931,600. The put catches up with the money-market hedge above 1.1418 and with the forward only above about 1.1988. That is 8% above today's spot, and the top of the range I modeled was 5%. Paying the premium on day one instead of at the end costs about USD 8,474 more, and it doesn't change which strategy wins at any rate I tested.

So the put buys protection against a fall and keeps some of a rally, and it is expensive for that, because the rally would have to be large before it pays for the premium.

## D. Recommendation

**Lock in the forward for the full EUR 11,000,000, once the bank confirms 1.1756 in writing.**

The forward at the quoted rate gives us USD 12,931,600, which beats every other strategy at every settlement rate I tested.

**The quote does not agree with the market.** The forward implied by today's spot and interest rates is 1.1194. The bank's quote is 1.1756. The difference is 0.0562 per euro, or USD 618,195 on our receivable (5.1% of spot).

I can't explain that gap with interest rates. At today's spot and a euro rate of 3.158%, a forward of 1.1756 would need a dollar rate of about 11.4%, and the actual rate is 4.5%. My best explanation is that 1.1756 is a stale indicative quote that did not move with the market: spot has dropped from 1.1224 to 1.1081 since the quote was set, and the gap widened from USD 472,000 to USD 618,000 as it did. I haven't asked the bank for a requote yet. I don't know whether the bank would deal at that rate.

This does not change which strategy I recommend. It changes how much it is worth and what has to happen first.
- If the bank confirms 1.1756 is firm for this amount, we collect USD 618,195 more than we could build ourselves. We should take it immediately.
- If the bank requotes near 1.1194, the forward and the money-market hedge become the same, at about USD 12.31 million. I would still pick the forward because it needs no EUR 10.7 million loan.
- Either way, we should not budget the extra USD 618,195 until the bank confirms the rate.

A put would be my choice only if the board thinks the euro is likely to rise more than about 8% in nine months and accepts a floor of USD 11.94 million. I have no evidence for that view, and I don't think we should pay USD 246,400 for it.

## E. Why this is right for us

- **Cash-flow stability.** The forward gives one known dollar amount on one known date.
- **Budget certainty.** Our euro receivable is certain, so a fixed rate fits it exactly. We don't need flexibility on an amount that won't change.
- **Liquidity.** The forward needs no payment today, apart from whatever margin the bank requires. The put takes USD 246,400 in cash up front, and the money-market hedge needs a EUR 10.7 million loan.
- **Optionality.** We give up gains if the euro rises. In the range I modeled, the put never made up for its premium against the forward, so I don't think that gain is worth buying.
- **Cost.** The forward costs nothing at the quoted rate. The put costs USD 246,400 and the short call costs us downside protection.

**Limits.** This uses simple interest, one rate per currency, and no fees or margin. The dollar rate is a Treasury yield interpolated to 273 days and the euro rate is a Euribor midpoint. The option premiums are the scenario's given figures and don't fit put-call parity at today's rates. None of these change the ranking.
