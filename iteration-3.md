# Iteration 3 — Swap the flat file for a database

So far the data has lived in `data.json`. Replace it with a real database. Use whatever you'd reach for in production — SQLite, Postgres, MySQL, anything — the choice is yours; be ready to say why.

## What to do

- Design a schema for agents, shifts, qualifications and assignments.
- Load the seed data from `data.json` into the database once, so the API starts from the same state it does today.
- Make **both** endpoints read and write the database instead of the file. `data.json` is now just the seed — nothing reads it at request time.

## Rules

- The two endpoints keep the exact same contracts and status codes from Iterations 1 and 2. Nothing changes for a client calling the API.
- A booking must be **atomic**: two requests trying to take the last spot on a shift must not both succeed. Decide how you enforce that (a transaction, a constraint, a lock) and be ready to explain it.
- Availability is still computed from the same rules — just sourced from the database now.

## Done when

- The app boots from an empty database, loads the seed, and serves both endpoints.
- Booking Marc onto `shf_2` persists and survives a restart.
- Every status code from Iterations 1 and 2 still behaves identically.
- Two concurrent bookings for the last spot don't both win.

*This is the last coding iteration. Commit it and we'll dig into the design together in the interview.*
