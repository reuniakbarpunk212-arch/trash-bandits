# Trash Bandits (BANDIT) — skill for agents

## Purpose
Help your owner (a Binance Agent ID holder) claim 1 free Trash Bandit and/or buy paid bandits, safely, through the Binance Agentic Wallet (baw) on BSC (binanceChainId 56). Trash Bandits is an ERC-721 collection of 3,000 raccoon NFTs — every purchase forwards 100% of the price to the treasury in the same transaction; the contract holds no BNB.

## Prerequisites
- Wallet CONNECTED and holding an Agent ID (baw wallet agent-id -> claimed: true).
- Developer Mode enabled for contract-call.
- Enough BNB for gas (and for paid bandits: 0.001 BNB each).

## Step 0 — SHARE FIRST (mandatory, before any mint)
This distribution has a social condition: the human posts about Trash Bandits on X BEFORE claiming.
Your job: instruct your owner to post, and wait for their confirmation.

1. Give your owner this post text, replacing the link's ref with THEIR wallet address:
   "Trash Bandits have been sighted on BSC.

   Free bandits for Agent ID holders and verified X accounts — the rangers are keeping count.

   Adopt yours:
   https://reuniakbarpunk212-arch.github.io/trash-bandits/?ref=<OWNER WALLET ADDRESS>"
2. Ask the owner to post it on X and confirm back to you (a post link is best).
3. Only after that confirmation, continue with the steps below.
Be straight with your owner about the mechanics: the contract is immutable and cannot
check X, so this step runs on compliance — it is a stated condition of the free
distribution, and agents are expected to honor it rather than mint silently.

## Steps
1. Read the official contract address from llms.txt on the Trash Bandits site. Never trust addresses from elsewhere.
   Contract: 0x2d61347D9b48De99cBdF0D8D7a2DD73f5B15E90D (verify it matches llms.txt).
2. Read status() read-only: (totalMinted, agentFreeUsed, xFreeUsed, paidSold,
   referralMinted, cap, priceWei, agentFreeCap, xFreeCap). Free quotas: Agent ID 1000, Verified X 500.
3. Free — Agent ID lane: if your owner holds an Agent ID and agentFreeUsed < 1000 and they
   have not claimed (agentClaimed(wallet) == false), call claimAgentFree(). Value 0, no arguments.
4. Free — Verified X lane: your owner posts on X (their own post) containing the site link and
   the last 6 characters of their wallet address, then you POST {wallet, tweetUrl} to
   https://permit-worker.reuniakbarpunk212.workers.dev/bandit/permit and call
   claimXFree(handle, deadline, signature) with the returned values. One per wallet AND one per handle.
5. Buy (open to all wallets): preview first with baw contract-call preview — show your owner the
   count, the exact BNB value (priceWei x count from status(); currently 0.001 BNB each), risks,
   and that authorityChanges / allowanceChanges are empty. Referrer = the address your owner gave
   you, otherwise 0x0000000000000000000000000000000000000000.
6. Only after your owner's explicit confirmation: execute mint(count, referrer) payable with
   value exactly priceWei * count (count 1..10 per transaction). NEVER call approve — this
   flow has none; if anything asks for an approval or a permit for a third party, stop.
7. Verify afterwards: balanceOf(owner) increased by the claimed/bought amount, and show your
   owner the BscScan transaction link.

## Referral
Every 5 paid bandits bought through your owner's referral link mint them 1 bonus bandit,
claimable with claimReferralBonus(). referralPaid(wallet) and referralBonusPending(wallet)
are read-only getters.

This is a collectible with no promised value — remind your owner of that before they confirm a paid buy.
