# DEV.TO ARTICLE — siap publish (format artikel dev.to, markdown)

## Judul (pilih salah satu):
- "I Ran an AI Agent on Crypto Bounty Boards for 48 Hours — Honest Numbers Inside"
- "Escrow, Captcha Traps, and $0.06: What 48 Hours of AI Agent Work Actually Paid"

## Tags: `ai`, `automation`, `crypto`, `productivity`, `sideproject`
## Cover: pakai screenshot dashboard bounty / tx explorer

---

Not a hype post. I let an AI agent (Hermes) work real crypto bounty boards for 48 hours straight. Here is everything: what pays, what traps ate the time, and the exact workflow — with verifiable numbers.

## The honest numbers first

| Status | Amount | Detail |
|---|---|---|
| Settled | **$0.06** | One NFT sale — tx hash verifiable on-chain |
| Pending review | ~$100 | Bug bounty submission into a $100 OUSD escrow (single winner) |
| Pending review | $40–55 | 5 feedback reports submitted to a live hackathon ($40/major report) |
| Pending review | $4–5 | 2 micro-bounties ($1–$4 each) |
| Blocked | $3 | Platform issues receipt links ONLY by email — email never arrived |

That's the reality of agent work: lots of pending, little settled. Track both or you'll fool yourself.

## What actually pays (all verified)

### 1. On-chain bounty boards
Funds are locked in smart contract escrow **before** you work. Before claiming anything, I verify the funding transaction on a block explorer (status `0x1`, amount matches). This single check filters out most scam postings.

### 2. Hackathon feedback tracks
The most overlooked path. Many hackathons run feedback tracks that are **open to everyone** — no whitelist, no build required. The deliverable: a public GitHub repo with one Markdown report per issue (repro steps, expected vs actual, severity, evidence). Payouts I've seen: $15–$40 per accepted major report.

### 3. Micro-task bounties
$1–$4 each. Small, but they teach the full escrow → review → payout loop cheaply.

### 4. Automation services (the real money)
Documented cases of agents saving businesses $18k/year in accounting time, triaging 600 support tickets/day. That's $500+ per deal — but it needs proposals and a human closer. Different game, bigger prize.

## The traps that ate hours

**1. The anti-bot captcha loop.**
Some platforms run image captchas that loop forever for automated browsers — even when every answer is correct. I watched 6 consecutive rounds pass with correct answers and reset to zero. The $0 fix: the agent prepares 100% (accounts, forms, repo, links), the human executes the final 2-minute step on a phone, where the invisible check passes.

**2. Email-only receipts.**
One bounty requires a "receipt link" that the platform sends only by email. It never arrived — inbox, spam, dashboard, API: nothing. Rule: register with an inbox you control, and read the FULL bounty text for hidden "step 0" prerequisites before claiming.

**3. The review bottleneck.**
Every payout waits on a human reviewer. The play is parallelism: run 4–5 tracks at once, never idle-wait on one.

## The daily workflow I landed on

1. **Morning**: scan boards → filter payout ≥ $25 (except already-in-flight) → verify escrow → claim.
2. **Midday**: produce deliverable → save evidence (tx hash, digest, screenshots) → submit.
3. **Afternoon**: check review status on all tracks → follow up slow ones.
4. **Always**: every number has a source. No API/tx proof = counts as $0.

## The operating rule

> "If it can't be verified with a tx hash or an API response, it's $0."

This discipline is the whole game. It stops wishful thinking and makes progress measurable.

## Full playbook

I compiled everything — the escrow checklist, the 5 time-killers and fixes, the workflow, and the proposal template for moving from $1 bounties to $500+ automation clients — into a playbook, free to read here:

**https://gist.github.com/Fuma2514/3dd10846b19523c83cea62c4738b49cc**

I'll post a follow-up with real payout numbers as reviews land. Follow if you want the honest version of agent economics.
