# XML Support Matrix

This matrix describes the compact XML profile implemented by the canister.

| Format | Route | Decode API | Validation/Application | Status |
| --- | --- | --- | --- | --- |
| `pain.001.xml` | `submitTransportEnvelope` | `decodePain001Xml` | `validatePain001Xml`, `submitPain001Xml` | implemented compact |
| `pain.008.xml` | `submitTransportEnvelope` | `decodeDirectDebitXml` | `validateDirectDebitXml` | implemented compact |
| `pacs.003.xml` | `submitTransportEnvelope` | `decodeDirectDebitXml` | `validateDirectDebitXml` | implemented compact |
| `pacs.008.xml` | `submitTransportEnvelope` | `decodePacs008Xml` | `validatePacs008Xml`, `auditPacs008Xml` | implemented compact |
| `pacs.009.xml` | `submitTransportEnvelope` | `decodePacs009Xml` | `validatePacs009Xml` | implemented compact |
| `cover.payment.xml` | `submitTransportEnvelope` | `decodeCoverPaymentXml` | `validateCoverPaymentXml` | implemented compact |
| `pain.002.xml` | `submitTransportEnvelope` | `decodeStatusReportXml` | `validateStatusReportXml` | implemented compact |
| `pacs.002.xml` | `submitTransportEnvelope` | `decodeStatusReportXml` | validates then applies payment state | implemented compact |
| `pacs.004.xml` | `submitTransportEnvelope` | `decodeStatusReportXml` | validates then applies return state | implemented compact |
| `camt.056.xml` | `submitTransportEnvelope` | `decodeInvestigationXml` | `validateInvestigationXml` | implemented compact |
| `camt.029.xml` | `submitTransportEnvelope` | `decodeInvestigationXml` | `validateInvestigationXml` | implemented compact |
| `pacs.028.xml` | `submitTransportEnvelope` | `decodeInvestigationXml` | `validateInvestigationXml` | implemented compact |
| `camt.110.xml` | `submitTransportEnvelope` | `decodeInvestigationXml` | `validateInvestigationXml` | implemented compact |
| `camt.111.xml` | `submitTransportEnvelope` | `decodeInvestigationXml` | `validateInvestigationXml` | implemented compact |
| `pain.013.xml` | `submitTransportEnvelope` | `decodeRequestToPayXml` | `validateRequestToPayXml` | implemented compact |
| `pain.014.xml` | `submitTransportEnvelope` | `decodeRequestToPayXml` | `validateRequestToPayXml` | implemented compact |
| `camt.055.xml` | `submitTransportEnvelope` | `decodeRequestToPayXml` | `validateRequestToPayXml` | implemented compact |
| `admi.002.xml` | `submitTransportEnvelope` | `decodeAdministrativeXml` | `validateAdministrativeXml` | implemented compact |
| `admi.004.xml` | `submitTransportEnvelope` | `decodeAdministrativeXml` | `validateAdministrativeXml` | implemented compact |
| `admi.007.xml` | `submitTransportEnvelope` | `decodeAdministrativeXml` | `validateAdministrativeXml` | implemented compact |
| `admi.011.xml` | `submitTransportEnvelope` | `decodeAdministrativeXml` | `validateAdministrativeXml` | implemented compact |
| `camt.053.xml` | `submitTransportEnvelope` | `decodeCamt053Xml` | statement-entry decode | implemented compact |
| `camt.054.xml` | `submitTransportEnvelope` | `decodeCamt054Xml` | notification-entry decode | implemented compact |

## Not Full XML Conformance Yet

The canister intentionally supports a strict subset:

- known message roots and paths only
- deterministic serializer output
- DTD/entity/external identifier rejection
- bounded raw XML size through `UsageGuideline.maxMessageBytes`
- stable rule IDs for negative fixtures

Missing for full market-grade XML conformance:

- XSD validation for every branch of each message family
- CBPR+/HVPS+/bank-specific usage-guideline differential runner
- canonical XML semantic hash
- namespace/version drift corpus
- official market-rule certification evidence
