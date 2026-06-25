# Contributing

This repository is a standards-facing example. Treat validation behavior as a
contract: every new rule should carry a stable `ruleId`, a precise path, and a
testable example.

Do not hard-code private rail rules into `ISO20022.mo`. Add them through
`UsageGuideline` configuration so bank, rail, HVPS+, and CBPR+ variants can
coexist.
