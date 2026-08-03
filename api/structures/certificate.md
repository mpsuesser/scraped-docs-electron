---
url: https://www.electronjs.org/docs/latest/api/structures/certificate
title: "Certificate"
description: ""
access_date: 2026-08-03T19:08:43.151Z
current_date: 2026-08-03T19:08:43.151Z
---

- `data` string - PEM encoded data
- `issuer` [CertificatePrincipal](certificate-principal.md) - Issuer principal
- `issuerName` string - Issuer's Common Name
- `issuerCert` Certificate - Issuer certificate (if not self-signed)
- `subject` [CertificatePrincipal](certificate-principal.md) - Subject principal
- `subjectName` string - Subject's Common Name
- `serialNumber` string - Hex value represented string
- `validStart` number - Start date of the certificate being valid in seconds
- `validExpiry` number - End date of the certificate being valid in seconds
- `fingerprint` string - Fingerprint of the certificate
