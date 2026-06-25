# Legacy-System Integration Notes

Native canister routes today:

- `mt103`: parsed by `decodeMt103` or submitted through
  `submitTransportEnvelope(format = "mt103")`.
- `mt940`: parsed by `decodeMt940` or submitted through
  `submitTransportEnvelope(format = "mt940")`.
- `mt942`: parsed by `decodeMt942` or submitted through
  `submitTransportEnvelope(format = "mt942")`.
- `csv.payments`: parsed by `decodeCsvPayments` or submitted through
  `submitTransportEnvelope(format = "csv.payments")`.
- `fixed.payments`: parsed by `decodeFixedWidthPayments` or submitted through
  `submitTransportEnvelope(format = "fixed.payments")`.

Documented adapter patterns:

- Bank-specific CSV/fixed-width variants: normalize to the education layout or
  add a signed profile pack before submission.
- SFTP/file-drop: treat every file as a connector envelope with monotonic
  sequence, `remoteId`, `traceId`, payload hash, and ACK/NACK receipt.

## MT103 Coverage

The parser already handles the tag grammar generically and maps the supported
payment subset into `pain.001`:

| MT field | Current behavior |
| --- | --- |
| `20` | message id |
| `32A` | value date, currency, amount |
| `50A/F/K` | debtor party/account lines when present |
| `52A` | debtor agent BIC |
| `52D` | parsed as a field, but requires a BIC-bearing line to become routable |
| `53A/B/D`, `54A/B/D`, `56A/D` | parsed as fields and preserved as remittance evidence when a value is present |
| `57A` | creditor agent BIC |
| `57D` | parsed as a field, but requires a BIC-bearing line to become routable |
| `59/F` | creditor party/account lines when present |
| `70` | remittance text |
| `71A` | charge bearer mapping: OUR/BEN/SHA to DEBT/CRED/SHAR note |
| `72` | preserved as sender-to-receiver remittance evidence |

The next native-code hardening step is MT202 and wider MT option mapping with
stable rule IDs for every option branch.
