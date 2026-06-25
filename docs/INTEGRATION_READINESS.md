# Integration Readiness

This document separates implemented integration surfaces from the remaining
work needed for a polished bank/middleware integration program.

## Implemented Now

- Compact XML decode/validate/export for `pain.001`, `pacs.008`, `pacs.009`,
  cover payment bundles, direct-debit forms (`pain.008`, `pacs.003`),
  `pain.002`, `pacs.002`, `pacs.004`, `camt.056`, `camt.029`, `pacs.028`,
  case-management forms (`camt.110`, `camt.111`), request-to-pay forms
  (`pain.013`, `pain.014`, `camt.055`), administrative forms (`admi.002`,
  `admi.004`, `admi.007`, `admi.011`), `camt.053`, and `camt.054`.
- Connector routing for every compact XML format above.
- Inbound `pacs.002` and `pacs.004` application by original message id and
  UETR, with duplicate and lifecycle rejection.
- MT103 bridge into `pain.001` intake.
- MT940/MT942 native statement decoders.
- CSV and fixed-width native payment-file decoders.
- Outbound XML queue with lease, retry, ACK/NACK, payload hash, and dead-letter
  evidence.
- Profile-pack discovery through `integrationProfilePacks()`, including local,
  CBPR-shaped, legacy, SEPA, Fedwire, FedNow, and CPMI research overlays.
- File-based integration kit under `integration-kit/`.
- External XSD/profile runner harness under `integration-kit/profile-runner`,
  including `source-manifest.json` official-source provenance for ISO base
  schemas, SEPA, CBPR+/HVPS+, Fedwire, FedNow, BIS CPMI guidance, and
  operator-supplied CBE/bank profiles.
- Public ISO base XSD fetcher and checksum report:
  `integration-kit/scripts/iso-xsd-fetch.py` and
  `integration-kit/profile-runner/iso-base-download-report.json`.
- Deployed-canister replay harness:
  `integration-kit/scripts/deployed-replay.py`.
- Signed fixture-bundle hash manifest under `integration-kit/fixtures`.

## Still Missing For Full XML Claims

Do not call the current XML layer full ISO 20022 conformance. The remaining gate
is an external profile oracle:

- official XSD bundles supplied to the external runner
- CBPR+/HVPS+/bank-specific profile differential runner rules
- CBE/EBC participant MIG and profile bundle before any Egypt RTGS/CBE
  conformance claim
- full-ISO fixture hardening: current real XSD report has `2` XSD-valid
  schema-backed fixtures and `21` schema-backed compact fixtures that fail ISO
  base XSD validation
- canonical XML semantic hash
- namespace/version drift corpus
- deployment replay tests against real canister state
- current/latest ISO schema-version drift maps for each supported message family

## Still Missing For Native Legacy Breadth

The integration kit documents these paths, but the canister does not natively
parse all of them yet:

- MT202 and broader correspondent banking flows
- full MT103 option mapping for every A/B/D/F/K branch
- bank-specific CSV and fixed-width profile variants

## Still Missing For Public Scheme Breadth

Public-source profile packs are now tracked. Remaining public-scheme work is now
external profile evidence and workflow hardening, not native compact form support:

- official SEPA, Fedwire, FedNow, CBPR+, and HVPS+ profile-rule bundles

Recommended production path: keep SFTP/MQ/file-normalization in an adapter,
submit only canonical connector envelopes to the canister, and preserve original
file hashes in the adapter manifest.
