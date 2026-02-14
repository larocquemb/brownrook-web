Public certificate repository (publish-safe)

This S3 prefix contains PUBLIC certificate material only.

Allowed:
- Public leaf certificates (.crt/.pem)
- Public intermediate/root certificates (.crt/.pem)
- Public keys (.pub)

NOT allowed (ever):
- Private keys (.key)
- PKCS#12 bundles (.p12/.pfx)
- Encrypted private keys
- Anything that enables signing/decryption/impersonation
