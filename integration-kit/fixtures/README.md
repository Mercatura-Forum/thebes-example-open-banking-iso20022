# Fixture Bundle Hashes

`fixture-bundle.json` is the signed hash manifest for the integration kit
fixture corpus. It covers XML fixtures, legacy file fixtures, profile packs,
connector templates, expected results, cookbooks, and the external profile map.

Generate or refresh it with:

```sh
integration-kit/scripts/fixture-bundle.py manifest
```

Sign with an institution or release private key:

```sh
integration-kit/scripts/fixture-bundle.py sign \
  --private-key /secure/path/fixture-signing-key.pem
```

Verify a signature:

```sh
integration-kit/scripts/fixture-bundle.py verify \
  --public-key integration-kit/fixtures/dev-fixture-signing-public.pem
```

The repository includes a development public key and matching signature for the
current fixture bundle. Production users should replace it with their own key.
