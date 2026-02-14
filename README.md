# BrownRook Web

High-availability public website for **brownrook.com** and **www.brownrook.com**.

This repository defines the architecture and operational model for the
organization’s public web presence. It is intentionally separated from
homelab / site infrastructure.

---

## Purpose

The apex domain must remain available even if:

- Residential IP changes
- Proxmox infrastructure is powered down
- `idc.brownrook.com` is offline for maintenance
- Homelab services are unavailable

This repository manages the always-on layer.

---

## Architecture

User
  ↓
Route53 (Alias A)
  ↓
CloudFront Distribution
  ↓
S3 Bucket (www.brownrook.com)

---

## Components

### S3

Bucket:
`arn:aws:s3:::www.brownrook.com`

- Region: ca-central-1
- Public access: Blocked
- Accessed via CloudFront (Origin Access Control)

---

### CloudFront

Distribution ID:
`EIBZ6KYH09OCB`

Serves:

- brownrook.com
- www.brownrook.com

TLS:

- AWS ACM public certificate (us-east-1)

---

### Route53

Records:

| Name | Type | Target |
|------|------|--------|
| brownrook.com | A (Alias) | CloudFront |
| www.brownrook.com | A (Alias) | CloudFront |

---

## Separation of Concerns

brownrook.com → Organizational landing site (high availability)

idc.brownrook.com → Physical site ingress (dynamic residential IP)

All internal and homelab services exist under: