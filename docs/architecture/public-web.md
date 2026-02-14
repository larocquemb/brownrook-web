# Public Web Architecture (brownrook-web)

## Purpose
Provide a highly-available public presence for:
- `brownrook.com`
- `www.brownrook.com`

This layer must remain online even if the IDC site (`idc.brownrook.com`) is offline.

## Components (AWS)

### CloudFront
- Distribution ID: `EIBZ6KYH09OCB`
- TLS: ACM certificate in `us-east-1`
- Viewer protocol policy: redirect HTTP → HTTPS
- Origin: private S3 bucket (access via OAC)

### ACM (us-east-1)
- Certificate ARN: `arn:aws:acm:us-east-1:933812607621:certificate/496c5b01-1dc9-4490-a33c-b65a9d72a275`
- SANs:
  - `www.brownrook.com`
  - `brownrook.com`
- Type: `AMAZON_ISSUED` (DV, auto-renewed by ACM)

### S3
- Bucket: `www.brownrook.com`
- Origin access: private; CloudFront reads via OAC (no public bucket access)
- Certificates page content is published under: `/certificates/`

### Route 53
- Hosted Zone ID: `Z04891073LFEUB14MX3A6`
- Apex and `www` point to CloudFront (A and AAAA aliases)
- Email posture (SPF/DKIM/DMARC) also lives here

## Public Certificates Page
- URL: `https://www.brownrook.com/certificates/index.html`
- Contains **public** certificates only (no private keys, no .p12/.pfx)

## Layering rule
- Public HA layer: `brownrook.com`, `www.brownrook.com` (CloudFront/S3)
- Site anchor layer: `idc.brownrook.com` and `*.idc.brownrook.com` lives in `brownrook-edge`
