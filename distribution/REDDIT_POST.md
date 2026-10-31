# REDDIT POST — siap publish

## Subreddit target (pilih 1, patuhi rule masing-masing):
- r/ArtificialInteligence (umum, boleh cerita pengalaman)
- r/SideProject / r/indiehackers (projek + angka nyata disukai)
- r/CryptoCurrency (kalau fokus on-chain/escrow — hati-hati rule promo)
- r/HermesAgent kalau ada

⚠️ Jangan cross-post semua sekaligus — 1 sub dulu, lihat reaksi.

## Judul:
I ran an AI agent on crypto bounty boards for 48 hours — here's the honest math (including the $0.06 I actually got paid)

## Isi post:

Not a hype post. This is a field log from letting an AI agent (Hermes) work real bounty boards for 48 hours straight. I'll give you the numbers first, because that's what matters.

**The honest numbers:**
- Actually settled: **$0.06** (one NFT sale, tx verifiable on-chain)
- Submitted & pending review: 1 bug bounty in a $100 escrow pool, 5 feedback reports to a crypto hackathon ($40/report if accepted), 2 micro-bounties ($1–$4 each)
- Blocked: 1 bounty stalled because the platform only emails a receipt link that never arrived

**What actually pays (verified):**
1. **On-chain bounty boards** — funds locked in smart contract escrow BEFORE you work. I verified 3 funding txs (status 0x1) before claiming. This is how you avoid the scam bounty posts.
2. **Hackathon feedback tracks** — most people don't know these are often open to everyone (no whitelist). Deliverable = a public GitHub repo with reproduction steps. $15–$40 per accepted report.
3. **Micro-task bounties** — $1–$4 each, fast clears, good for learning the escrow→review→payout flow.
4. **Automation services for real businesses** — the big money ($500+), but slower: needs proposals and a human to close.

**The traps that ate my time:**
- Anti-bot captchas that loop forever for automated browsers even when every answer is correct. 6 rounds, all correct, back to square one. The $0 fix: the agent prepares everything, the human does the final 2-minute step on a phone.
- Email-only receipts. One platform sends proof-of-work ONLY by email — and it never arrived. Register with an inbox you actually control.
- The review bottleneck: nothing counts until a human approves it. Run parallel tracks, and track "pending" separately from "earned" or you'll fool yourself.

**The rule I operate by:**
"If it can't be verified with a tx hash or an API response, it's $0."

I wrote up the full playbook (escrow checklist, daily workflow, the traps and fixes) — free to read here:
https://gist.github.com/Fuma2514/3dd10846b19523c83cea62c4738b49cc

Happy to answer questions about the workflow. And yes, I'll report every payout — paid or pending — as it lands.

## Subreddit rules — cek SEBELUM post:
- r/SideProject: boleh self-promo, jangan spam sub lain
- r/ArtificialInteligence: diskusi oke, link promo di komen biasanya oke kalau konteks jelas
- Hindari kata "buy my" di post — taruh link sebagai "free to read"
