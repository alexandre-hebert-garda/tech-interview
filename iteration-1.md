# Iteration 1 — Who is available?

**Goal:** Return the agents who can actually work a given shift.

## Endpoint

```
GET /api/shifts/{id}/available-agents
```

An agent is **available** for a shift only when all of the following hold:

1. **Qualified.** They hold *every* qualification in the shift's `requiredQualifications`.
2. **Not expired.** Each of those required qualifications has an `expiresOn` on or after the shift's `start`. (Priya's `GUARD_LICENSE` expired 2026-08-31, so she can't work the September shifts.)
3. **Not double-booked.** They aren't already assigned (in `assignments`) to a shift whose time window **overlaps** this one. (Jordan is assigned to `shf_1`, 22:00–06:00, which overlaps `shf_2`, 23:00–03:00 — so Jordan is out for `shf_2`, and out of `shf_1`'s own list too, since he's already on it.)

## Example

```
GET /api/shifts/shf_2/available-agents
```

`shf_2` requires `GUARD_LICENSE` and `CROWD_CONTROL`. Only Marc holds both, is current, and is free:

```json
[
  { "id": "agt_3", "name": "Marc Tremblay" }
]
```

`shf_1` requires only `GUARD_LICENSE`, yet `GET /api/shifts/shf_1/available-agents` also returns just Marc — once the expired (Priya) and double-booked (Jordan) agents are excluded.

## Rules

- Unknown shift id → `404`.
- A shift with no `requiredQualifications` → the qualification check passes for everyone (expiry and double-booking still apply).
- The response is a JSON array — an empty array if nobody is available, not `404`.
- Two shifts overlap if one starts before the other ends. Shifts that only touch (one ends exactly when the next starts) do **not** overlap.
- Handle overnight shifts correctly (a 22:00→06:00 shift is 8 hours, not −16).

## Done when

- `shf_1` and `shf_2` both return just Marc.
- Agents with an expired required qualification are excluded.
- Agents booked on an overlapping shift are excluded.
- A missing shift returns `404`.

*When this works and is committed, move on to Iteration 2.*
