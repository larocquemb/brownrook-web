# CloudFront + S3 Configuration (brownrook.com / www.brownrook.com)

## Goal

Keep `brownrook.com` and `www.brownrook.com` online even when the IDC homelab
(`idc.brownrook.com`) is offline, under maintenance, or the residential IP changes.

## High-level Architecture

Route 53 (Alias A/AAAA)
  → CloudFront Distribution
    → S3 bucket `www.brownrook.com` (static content)
      + optional public artifacts under `s3://www.brownrook.com/certificates/` (public certs only)

## AWS Resources

- S3 bucket (origin):
  - ARN: `arn:aws:s3:::www.brownrook.com`
  - Purpose: store static website content + public artifacts
  - Public access: blocked (private bucket)

- CloudFront distribution:
  - Distribution ID: `EIBZ6KYH09OCB`
  - Serves: `brownrook.com`, `www.brownrook.com`

- TLS certificate:
  - AWS ACM public cert (must be in `us-east-1` for CloudFront)
  - Covers: `brownrook.com` and `www.brownrook.com`

## DNS (Route 53)

- `brownrook.com` → A/AAAA Alias to CloudFront distribution
- `www.brownrook.com` → A/AAAA Alias to CloudFront distribution

This ensures the public site does not depend on the residential IP or homelab uptime.

## S3 Configuration Notes

- Bucket: `www.brownrook.com`
- Public access block: enabled (recommended)
- Website content: `index.html` (and other static assets)

### Public certificates prefix

Prefix: `s3://www.brownrook.com/certificates/`

Policy:
- PUBLIC CERTIFICATE MATERIAL ONLY (safe to publish)
- Never store private key material here

Disallowed (never):
- `.key`
- `.p12` / `.pfx`
- anything containing private keys

A README is stored at:
- `s3://www.brownrook.com/certificates/README.txt`

## CloudFront Configuration Notes (what to record)

Document/verify the following in snapshots:

- Aliases (CNAMEs): `brownrook.com`, `www.brownrook.com`
- Viewer protocol policy (recommended: redirect HTTP → HTTPS)
- Allowed methods (typically GET/HEAD for static)
- Default root object (often `index.html`)
- Cache policy (managed or custom)
- Compression (recommended: enabled)
- Origin access:
  - OAC (preferred) or OAI (legacy)
- Logging (optional)
- Error responses (optional: SPA routing or custom 404)

## Operational Commands

Get CloudFront domain name:

```bash
aws cloudfront get-distribution \
  --id EIBZ6KYH09OCB \
  --query "Distribution.DomainName" \
  --output text