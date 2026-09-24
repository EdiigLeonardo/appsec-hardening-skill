# Secrets and key material

Limit secrets to the minimum trusted principals and required lifetime.

Review:

creation; storage; distribution; access; rotation; revocation; logging; build-time exposure; client exposure.

Rule:

A secret available to an untrusted client is not a secret.

Prefer short-lived credentials and scoped capabilities.

## Cryptographic use

Review:

purpose of cryptography; key management; random generation; nonce/IV handling; signing vs encryption; verification; expiration; algorithm agility.

Do not invent custom cryptographic protocols.
