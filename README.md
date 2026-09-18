# ProofPatch Assurance Publisher

Canonical independent primary assurance publisher for ProofPatch V3.

Authority identity:

`blaq2uu/proofpatch-assurance`

Approved raw prefix after repository creation:

`https://raw.githubusercontent.com/blaq2uu/proofpatch-assurance/`

## Role

This repository publishes release-specific, immutable, commit-pinned assurance
evidence after a ProofPatch candidate has been installed provisionally and the
configured observation delay has elapsed.

Evidence is never accepted merely because this publisher says "PASS".
ProofPatch validators independently compare the raw expected/observed facts and
derive the assurance boolean vector themselves.

See `ASSURANCE_EVIDENCE_SCHEMA.md`.

## Publication rules

- Public repository.
- Evidence files are JSON.
- Every evidence URL used on-chain must include a 40-character lowercase commit
  SHA in the raw GitHub URL.
- Never reuse an `evidence_id`.
- Primary and corroboration evidence IDs must differ.
- `published_at` and `expires_at` are Unix seconds.
- Evidence must be fresh under the target's `max_evidence_age_seconds`.
- Do not rewrite or silently replace already-referenced evidence.
