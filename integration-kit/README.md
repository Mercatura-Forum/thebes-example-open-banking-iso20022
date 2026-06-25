# ISO 20022 Integration Kit

This kit is the hands-on entry point for banks, fintechs, middleware teams, and
legacy-file integrators.

The canister has compact-profile XML support for every message family listed in
`XML_SUPPORT_MATRIX.md`. It does not claim full ISO 20022 XSD/profile
conformance. Full market-rule validation is the next external oracle gate:
signed fixture bundles plus an off-chain XSD/profile differential runner.

## Contents

- `xml/valid`: XML files that should decode under the compact profile.
- `xml/invalid`: XML and replay fixtures expected to dead-letter or fail with
  stable rule IDs.
- `connectors`: Candid envelope templates, registration snippets, batch manifest
  format, and file-drop adapter notes.
- `legacy`: MT103, MT940/MT942, CSV, and fixed-width examples for legacy
  integration planning.
- `profiles`: named profile-pack metadata for local, legacy, SEPA, Fedwire,
  FedNow, and CPMI research overlays.
- `candid`: copyable `thebes-deploy` call/query snippets and the `.did` interface.
- `expected`: expected statuses and rule IDs for replay checks.
- `cookbooks`: operator flows such as file intake, status return, outbound
  leasing, and dead-letter repair.
- `profile-runner`: external XSD/profile runner map for full XML evidence.
- `fixtures`: signed fixture-bundle hash manifest and verification key.

## Current Truth

Implemented compact XML routes:

- `pain.001.xml`
- `pain.008.xml`
- `pacs.003.xml`
- `pacs.008.xml`
- `pacs.009.xml`
- `cover.payment.xml`
- `pain.002.xml`
- `pacs.002.xml`
- `pacs.004.xml`
- `camt.056.xml`
- `camt.029.xml`
- `pacs.028.xml`
- `camt.110.xml`
- `camt.111.xml`
- `pain.013.xml`
- `pain.014.xml`
- `camt.055.xml`
- `admi.002.xml`
- `admi.004.xml`
- `admi.007.xml`
- `admi.011.xml`
- `camt.053.xml`
- `camt.054.xml`

Implemented legacy routes:

- `mt103`
- `mt940`
- `mt942`
- `csv.payments`
- `fixed.payments`

Documented adapter patterns, not native canister parsers yet:

- SFTP/file-drop batch transport
- bank-specific CSV/fixed-width variants beyond the education layout
- full SWIFT option coverage beyond the supported parser subsets
- rail-specific acknowledgement SLA/state beyond compact `admi` validation and
  C7 workflow correlation

## Minimal Replay Order

1. Register a connector that allows the target format.
2. Submit `xml/valid/pain001-eg-domestic.xml` as `pain.001.xml`.
3. Dispatch the stored payment with `dispatchPacs008(paymentId)`.
4. Submit `xml/valid/status-pacs002-settled.xml` as `pacs.002.xml`.
5. Submit `xml/invalid/status-uetr-mismatch.xml` as `pacs.002.xml` and confirm
   `STATUS-UETR-MISMATCH`.
6. Submit the same valid `pacs.002.xml` again and confirm
   `STATUS-PACS002-DUPLICATE`.
7. Query `paymentXmlBundle(paymentId)`, `camt054Notification(paymentId)`,
   `auditProof(auditId)`, and `secondaryIndexHealth()`.

Use `expected/replay-results.md` as the operator checklist.

## External Profile Evidence

Fetch public ISO base XSDs:

```sh
integration-kit/scripts/iso-xsd-fetch.py
```

List the required official schema files and their public/account-gated source
packs:

```sh
integration-kit/scripts/xsd-profile-runner.py --list-required-schemas
```

Run XSD/profile checks with supplied official schemas:

```sh
integration-kit/scripts/xsd-profile-runner.py \
  --schema-dir /path/to/iso20022/xsd \
  --require-all-schemas \
  --strict-source-manifest
```

`profile-runner/source-manifest.json` records official-source provenance and
operator actions. It is metadata only; the repo still expects the certification
environment to supply ISO, Swift/MyStandards, EPC, Federal Reserve, CBE/EBC, or
bank-specific artifacts.

Current checkpoint: the public ISO base XSDs are downloaded locally and the
runner executes real XSD checks. The report is
`profile-runner/profile-report.json`; the current compact fixture corpus has
`2` XSD-valid schema-backed fixtures and `21` schema-backed fixtures that still
need full-ISO fixture hardening.

## Deployed Canister Replay

Run the actor-level replay harness against a deployed canister or local alias:

```sh
integration-kit/scripts/deployed-replay.py --canister iso20022 --dry-run
integration-kit/scripts/deployed-replay.py --canister iso20022
```

Deploy and smoke-test against the cluster:

```sh
thebes-deploy --manifest thebes.toml deploy iso20022
thebes-deploy --manifest thebes.toml call iso20022 claimOwner
integration-kit/scripts/deployed-replay.py --canister iso20022
```

If a live replay stops after creating a payment, resume without submitting a
duplicate `pain.001`:

```sh
integration-kit/scripts/deployed-replay.py \
  --canister iso20022 \
  --payment-id 0 \
  --skip-dispatch
```

The harness calls discovery/readiness queries, validates XML fixture blobs
through canister query methods, registers the file connector, submits a real
`pain.001` fixture, dispatches the generated `pacs.008`, submits valid and
mismatched `pacs.002` connector envelopes, then queries payment phases, XML
bundle evidence, audit tip, secondary-index health, and dead letters.

The runtime canister has one active guideline at a time. Public SEPA/CBPR+/Fedwire
education fixtures may return expected country/settlement-method errors when the
default Egypt guideline is active; treat that as profile-selection evidence, not
as a deployment failure.

## C6 Certified Disclosure

Verify the certified disclosure commitment from outside the canister:

```sh
integration-kit/scripts/certified-disclosure-verify.py --canister iso20022
```

The helper drives the deployed hub through `thebes-deploy --json query`, reading
the disclosure methods' JSON twins (`certifiedAuditDisclosureJson` et al.). It
recomputes the audit snapshot hash, balance snapshot hashes, balance Merkle root,
and final disclosure root, and — when an IC-format certificate is present —
checks the certificate hash-tree leaf for `certified_data`. The remaining
signature check is deployment-specific: IC root-key/BLS for ICP replay, and a
Thebes network proof for Thebes production.

Refresh and verify fixture bundle hashes:

```sh
integration-kit/scripts/fixture-bundle.py manifest
integration-kit/scripts/fixture-bundle.py verify \
  --public-key integration-kit/fixtures/dev-fixture-signing-public.pem
```
