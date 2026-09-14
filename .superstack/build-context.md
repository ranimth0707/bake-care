# Build context

## Arisan winner elimination — 2026-09-14

- Production program: `Dwd7DXUQHRJaj1suYz6fTcVW7JJqBFVztg1z77t6Ysg` on Cookie Chain.
- Production frontend: `https://arisan-cook.vercel.app`.
- Winners are tracked in a companion `CircleRoster` PDA so existing `Circle`
  accounts keep their original binary layout.
- New circles create a ready roster at `start_circle`. Legacy running circles
  lazily initialize and sync `Member.has_won` records before their next draw.
- `finalize_turn` maps randomness only across seats absent from the winner
  bitmap. `claim_turn` updates both the Member and roster in one transaction.
- A legacy invalid result can be redrawn immediately when the selected member
  has already won, is inactive, or has not paid the current round.
- Frontend transactions remain sponsored; the maximum legacy migration path is
  reimbursement + initialize + sync + request/finalize (four instructions).
- Economic constraint: in an N-member ROSCA, every member commits to N payments.
  Optional collateral covers only the number of contributions it contains;
  payments after collateral is exhausted cannot be forced from a wallet.
- Rollback binary for the pre-upgrade program was saved temporarily at
  `/tmp/arisan-upgrade.SddVVY/cookie_jar-before.so`.

