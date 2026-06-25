# XSD Validation Checkpoint

Date: 2026-06-23 UTC

This checkpoint records the first real external XSD run using public ISO 20022
base schemas downloaded from the ISO catalogue/archive.

## Inputs

- Schema cache:
  `integration-kit/profile-runner/official-schemas/iso-base/`
- Schema download report:
  `integration-kit/profile-runner/iso-base-download-report.json`
- XSD/profile runner report:
  `integration-kit/profile-runner/profile-report.json`
- Fixture map:
  `integration-kit/profile-runner/profile-map.json`

The schema cache is intentionally ignored by source control. The download report
records URLs, byte counts, and SHA-256 hashes for handoff/replay evidence.

## Result

- Required ISO base schema files: `22`
- Downloaded ISO base schema files: `22`
- Missing source metadata: `0`
- Schema-backed fixture results: `23`
- XSD-valid fixture results: `2`
- XSD-invalid fixture results: `21`
- Custom/non-XSD profile results: `2`

The two XSD-valid fixtures are:

- `valid/status-pacs002-settled.xml`
- `invalid/status-uetr-mismatch.xml`

The second one is intentionally XSD-valid but business-invalid because the UETR
does not match the stored payment.

## Interpretation

The external standard gate has moved forward: the repo now has a reproducible
path to fetch ISO base schemas and run real XSD checks. The current compact
fixtures are still canister-oriented examples, not full ISO-conformant market
fixtures.

Most invalid results are expected compact-profile gaps:

- compact postal-address ordering versus ISO schema order
- compact payment type fields that need nested ISO choice elements
- BAH plus Document fixtures that need profile envelope handling
- simplified investigation, request-to-pay, cash-management, and administrative
  roots compared with their ISO schema roots
- missing mandatory group header and status/case assignment elements in compact
  examples

## Commands

Fetch public ISO base XSDs:

```sh
integration-kit/scripts/iso-xsd-fetch.py
```

Run XSD validation:

```sh
integration-kit/scripts/xsd-profile-runner.py \
  --schema-dir integration-kit/profile-runner/official-schemas/iso-base \
  --require-all-schemas \
  --strict-source-manifest \
  --out integration-kit/profile-runner/profile-report.json
```

Run deployed-canister replay harness:

```sh
integration-kit/scripts/deployed-replay.py \
  --canister iso20022
```

Use `--dry-run` first to inspect the exact `thebes-deploy call`/`query` commands
without submitting updates.
