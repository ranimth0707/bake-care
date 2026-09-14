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
- `CircleSafety` is a second companion PDA. New circles require each member to
  lock `contribution × max_members` as reserve before creation can start. The
  safety PDA and bond vault are checked before request/finalize/claim.
- `slash_absent` now settles a missed round even when the available reserve is
  zero, increments `paid_this_round`, and moves only the available amount. A
  protected circle cannot reach that state for a future obligation: the draw is
  blocked until reserves are repaired.
- After all members settle a round, the required reserve drops by that round's
  obligation before the draw. This keeps a fully collateral-funded default from
  failing merely because the just-settled amount moved from Bond to Pot.
- The config PDA now has an explicit `transfer_authority` handoff for a Squads
  governance PDA. The deployed upgrade authority has not been transferred yet;
  use a 2-of-3 threshold with a documented recovery plan.
- Rollback binary for the pre-upgrade program was saved temporarily at
  `/tmp/arisan-upgrade.SddVVY/cookie_jar-before.so`.
