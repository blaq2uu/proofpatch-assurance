# ProofPatch V3 Assurance Evidence Schema

Contract schema identifier:

`proofpatch-assurance-v1`

The ProofPatch V3 fact-review engine requires the evidence document to contain
at least these top-level fields:

```json
{
  "schema": "proofpatch-assurance-v1",
  "kind": "assurance_primary OR assurance_corroboration",
  "evidence_id": "unique-id-at-least-8-bytes",
  "issuer": "registered authority identity",
  "target": "0x...",
  "release_id": "release-...",
  "proposal_id": 0,
  "candidate_sha256": "64 lowercase hex chars",
  "policy_fingerprint": "64 lowercase hex chars",
  "manifest_sha256": "64 lowercase hex chars",
  "published_at": 0,
  "expires_at": 0,
  "facts": {
    "state_readbacks": [
      {
        "name": "exact manifest check name",
        "expected": "raw expected value",
        "observed": "raw observed value"
      }
    ],
    "canaries": [
      {
        "name": "exact manifest canary name",
        "expected": "raw expected value",
        "observed": "raw observed value"
      }
    ],
    "runtime_observations": [
      {
        "name": "runtime_output",
        "expected": "raw expected value",
        "observed": "raw observed value"
      }
    ],
    "security_observations": [
      {
        "name": "security_regression_scan",
        "expected": "raw expected value",
        "observed": "raw observed value"
      }
    ],
    "recovery": {
      "release_id": "precommitted recovery release id",
      "code_hex": "hex bytes of the precommitted recovery capsule"
    }
  }
}
```

## Exact validator semantics

For each primary/corroboration document, ProofPatch requires:

- `schema == "proofpatch-assurance-v1"`.
- `kind` is exactly `assurance_primary` or `assurance_corroboration` according
  to the publisher role.
- `evidence_id` exactly matches the ID submitted to ProofPatch.
- `issuer` exactly matches the authority bound in target registration.
- `target`, `release_id`, `proposal_id`, `candidate_sha256`,
  `policy_fingerprint`, and `manifest_sha256` exactly match the active
  proposal/release.
- `published_at <= review_now`.
- `review_now - published_at <= max_evidence_age_seconds`.
- `expires_at >= review_now` and `expires_at >= published_at`.
- `facts` has exactly these groups:
  `state_readbacks`, `canaries`, `runtime_observations`,
  `security_observations`, `recovery`.
- Every state/canary fact is a raw `{name, expected, observed}` triple whose
  name is required by the proposal's assurance manifest.
- Runtime contains exactly the `runtime_output` observation.
- Security contains exactly the `security_regression_scan` observation.
- Recovery contains exactly `release_id` and `code_hex`.

The validator then independently derives:

- installed hash binding
- kernel binding
- governor binding
- critical-state preservation
- interface requirements
- canary requirements
- runtime evidence validity
- no post-install security regression
- recovery path liveness
- assurance manifest satisfaction

The primary and corroboration publishers must produce raw evidence that yields
the same independently-derived vector. A publisher-provided PASS/FAIL verdict
is not authoritative.
