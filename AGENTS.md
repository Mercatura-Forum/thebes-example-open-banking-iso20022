# AGENTS.md -- ISO 20022 example canister

## Layout

```
thebes.toml                 deploy manifest
motoko/main.mo              backend actor: admin, guideline config, audit store
motoko/ISO20022.mo          pure ISO 20022 types and validators
motoko/ISO20022Xml.mo       strict XML subset codec and serializers
motoko/Connector.mo         on-chain connector envelope verifier
motoko/BloomFilter.mo       advisory duplicate prefilter
motoko/AuditMMR.mo          compact MMR audit accumulator
motoko/InPlaceSha256d.mo    vendored loop-based in-place SHA-256d hasher
motoko/test/                Motoko tests for validators and data structures
motoko/thebes-lib/          vendored backend library
```

## Toolchain

- Motoko compiler 1.4.1, fetched by `mops install`.
- Mops with local `thebes-lib = "./thebes-lib"`.
- No external SHA dependency; audit hashing uses the vendored LiteCoin-node
  allocation-free Merkle/SHA contribution.
- `thebes-deploy` for Thebes deployment.

## Compile Check

```sh
cd motoko
mops install
"$(ls "$HOME/.cache/mops/moc/1.4.1/moc" "$HOME/Library/Caches/mops/moc/1.4.1/moc" 2>/dev/null | head -1)" --check $(mops sources) main.mo
```

## Deploy

```sh
thebes-deploy identity new me
thebes-deploy deploy iso20022
```

## Public Methods

| Method | Kind | Notes |
| --- | --- | --- |
| `claimOwner` / `transferOwner` / `addAdmin` / `removeAdmin` | update | Admin surface from `thebes-lib`. |
| `setPaused` / `isPaused` | update / query | Emergency stop for writes. |
| `getGuideline` / `setGuideline` / `resetGuidelineToEgyptEducationBaseline` / `resetGuidelineToCrossBorderEducationBaseline` | query / update | Active usage-guideline data. |
| `getComplianceProfile` / `setComplianceProfile` / `resetComplianceProfile` | query / update | Deterministic AML/CFT screening profile. |
| `validatePain001` / `validatePacs008` | query | Returns structured validation issues without writing state. |
| `validatePacs009` / `validateCoverPayment` / `validateInvestigation` / `validateRequestToPayXml` / `validateDirectDebitXml` / `validateAdministrativeXml` | query | Cross-border, cover, investigation, request-to-pay, direct-debit, and administrative verifiers. |
| `screenPacs008` / `screenPacs009` / `screenCoverPayment` | query | Compliance screening reports. |
| `registerConnector` / `setConnectorActive` / `submitTransportEnvelope` | update | Connector registry and inbound envelope processing. |
| `getTransportRecord` / `listTransportRecords` / `listDeadLetters` | query | Connector audit/dead-letter reads. |
| `decodePain001Xml` / `validatePain001Xml` / `decodeRequestToPayXml` / `decodeDirectDebitXml` / `decodeAdministrativeXml` | query | Strict supported-subset XML import and validation. |
| `pain001ToXml` / `directDebitToXml` / `administrativeToXml` / `pacs008ToXml` / `pacs009ToXml` / `coverPaymentToXml` / `investigationToXml` / `requestToPayToXml` / `statusReportToXml` / `complianceReportToXml` | query | XML export helpers. |
| `submitPain001Xml` | update | Hub intake from XML, preserving raw XML hash in audit. |
| `paymentXmlBundle` / `camt053Xml` / `camt054Xml` | query | XML exports for stored payments and reporting. |
| `decodeMt103` / `decodeMt940` / `decodeMt942` / `decodeCsvPayments` / `decodeFixedWidthPayments` | query | Legacy payment and statement file parsers. |
| `submitPain001` / `submitDemoPain001` | update | Hub intake, transform, customer status, audit, and state storage. |
| `dispatchPacs008` / `acknowledgePacs002` / `returnPayment` | update | Operator lifecycle transitions. |
| `camt053Statement` / `camt054Notification` | query | Reporting views. |
| `verifyPaymentPhases` | query | Phase verifier reports. |
| `duplicateSignalFor` / `duplicateFilterInfo` | query | Bloom telemetry and exact duplicate status. |
| `auditPacs008` | update | Validates and stores an audit record, including invalid reports. |
| `demoPain001` / `demoPacs008` / `validateDemoPain001` / `validateDemoPacs008` | query | Built-in valid sample path. |
| `supportedStandards` / `capabilities` / `integrationProfilePacks` | query | Standards, profile packs, and implementation metadata for reviewers. |
| `auditTip` | query | Current audit count, last hash, Merkle root, and MMR root. |
| `auditProof` / `verifyAuditChain` | query | Inclusion proof and hash-chain self-check. |
| `getAudit` / `auditCount` / `listAudits` / `listAuditViews` | query | Bounded audit reads. |
