# AI Appendix — ECON 5200 Problem Set 1

## Shared AI Conversation

## Prompt

[Prep] Act as a partner reviewing an analytics deliverable before it
goes to a client board. You are looking for the objection that would
embarrass us in the room.

[Request] I audited a retailer's "average basket value" metric. I found
the naive mean was inflated by a right-skewed B2B tail and by a change
in what was logged between years. I recommend the dashboard show a
trimmed mean with an explicit B2B exclusion rule. Give me the three
strongest objections to that recommendation, ordered by how likely the
client is to raise them, and for each one tell me what evidence would
settle it.

[Iterate] Be specific to this metric. Do not give me generic data
quality advice.

[Mechanism Check] For each objection, say whether it is a question
about the data, about the statistic, or about the business definition
of the metric -- these need different kinds of answer.

[Evaluate] Which single additional analysis would most reduce our
exposure?

---

## Raw AI Response
Objection 1: "B2B buyers are our customers. Why are you deleting them from a revenue metric?"

Category: business definition. This is the most likely objection, and it will probably come from the CFO. Average basket value is usually read as one half of an identity: revenue = transactions × average basket. Once B2B is excluded, the dashboard number no longer multiplies back to reported revenue. Someone will notice that within the first ten minutes. The underlying question is not whether the tail is skewed. It is what decision this metric is supposed to inform. If it drives consumer pricing, promotions, or store staffing, excluding B2B is defensible. If it is a top-line health indicator, the exclusion looks like a quieter, better-looking number.

A second weakness sits inside this one. How is "B2B" identified? If it comes from an account flag or a tax-exempt ID, the rule is clean. If it is inferred from basket size or order pattern, then the exclusion and the trim are cutting the same transactions twice, and the rule is circular.

What settles it:

The client names the decisions the metric feeds.
You show a segmented view (B2C basket, B2B basket, mix share) that reconciles exactly to total revenue.
You document that the B2B classifier is independent of basket size, and report its agreement with any ground-truth flag.
Objection 2: "So is basket value up or down versus last year?"

Category: data. This is the one that would embarrass us, because the recommendation as written doesn't answer it. You found two problems, but the fix addresses only the first. A change in what was logged is a level break in the series, not an outlier problem. A trimmed mean computed across two logging regimes is just as non-comparable year over year as the naive mean. Boards look at trends, not levels. If the trimmed, B2B-excluded number shows growth, the first question will be how much of that growth is the logging change. Possible sources include returns or voids netted differently, shipping or tax now included, split shipments recorded as separate baskets, or gift card loads counted as purchases.

What settles it: Restate one year under the other year's logging rules. Ideally use an overlap period where both definitions can be computed on the same transactions. Report the size of the break directly: the old definition versus the new definition, same period, same population.

Objection 3: "Why 5% (or whatever cut you used)? Did you pick the trim that gave the answer you wanted?"

Category: statistic. This will come from an analytically minded director or the audit committee. Three specific weaknesses:

The trim is redundant or arbitrary. If B2B is already excluded by rule, the trim is now removing genuine large consumer baskets: holiday, big-ticket, your best customers. That may be exactly the signal management cares about.
The trim cuts both tails. A symmetric trim also drops small baskets and near-zero transactions, which may be returns artifacts or the logging issue itself.
The number has no clean business meaning. "Mean of the middle 90% of non-B2B baskets" cannot be explained to a board in one sentence. It also doesn't aggregate across stores or regions.

What settles it: A sensitivity table, by year, across trim levels (0, 1, 5, 10%) with and without the B2B exclusion, alongside the median and a winsorized mean. The question is not whether the levels differ. It is whether the trend and the ranking of stores or segments are stable across choices. If they are stable, the choice of statistic is a presentation detail. If they are not, the trim is driving the conclusion, and you need to say so.

The single analysis that most reduces exposure

A reconciliation waterfall from the old reported figure to the new recommended figure, on a consistent logging definition. Start with last year's and this year's naive mean as previously reported. Then decompose the gap into four bars:

Logging-definition change
B2B mix shift
Trim effect
Like-for-like consumer basket change

This one exhibit answers all three objections:

It reconciles to revenue (Objection 1).
It isolates the logging break, which forces you to do the restatement (Objection 2).
It shows how much the trim actually contributes (Objection 3). If that bar is small, the trim objection disappears. If it is large, you have found the real argument before the client does.

It also changes the conversation in the room. Instead of "we replaced your metric," the story becomes "here is exactly where the old number's movement came from." That framing is much harder to attack.

## Changes I Made

1. I selected **Objection 2, the logging-definition change**, as the
strongest objection because it directly challenges whether the two
years are comparable.

2. I implemented a consistent-definition analysis by restoring the
missing Year 2 transactions and excluding B2B orders before recomputing
the average basket value.

3. I used a numerical reconciliation instead of a full waterfall chart
because the existing analysis already quantified the logging effect and
the B2B effect separately.

4. The corrected mean was **$67.88 in Year 1 and $65.52 in Year 2**,
showing that the consumer basket value decreased rather than increased.

5. I did not use trimming as the main correction. I kept the
**arithmetic mean after explicit B2B exclusion and consistent logging**
because the evidence showed that the measurement definition was the
main problem.
