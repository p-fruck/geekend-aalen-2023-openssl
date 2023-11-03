---
title: "Geekend Aalen 2023 - Introduction to OpenSSL"
date: "3. November 2023"
author: "philipp"
---

# Geekend Aalen 2023 - Introduction to OpenSSL

- name: philipp
- studied: something with cyber cyber
- interested in: security
- **I AM NOT** an expert in cryptography
- **DO NOT TRUST** anything I say without doing your own research ;)

<!-- stop -->

For cryptographic recommendations, I rely on the NIST and BSI 
- [NIST: Cryptographic Standards and Guidelines](https://csrc.nist.gov/projects/cryptographic-standards-and-guidelines)
- [NIST: Transitioning the Use of Cryptographic Algorithms and Key Lengths](https://csrc.nist.gov/pubs/sp/800/131/a/r2/final)
- (German) [BSI TR-02102 Kryptographische Verfahren: Empfehlungen und Schlüssellänge](https://www.bsi.bund.de/DE/Themen/Unternehmen-und-Organisationen/Standards-und-Zertifizierung/Technische-Richtlinien/TR-nach-Thema-sortiert/tr02102/tr02102_node.html)
- Or use https://www.keylength.com/en/ to get a quick overview of multiple recommendations of different institutions

---

## Content

- Overview of file extensions associated with OpenSSL
- Generating your own certificate
- Creating a CA infrastructure
- Validating certificates using a certificate chain
- Securly serving content using TLS encryption

---

## File extensions

- `.key`: private key used to generate the certificate/CSR
- `.csr`: certificate signing request (to be signed by the CA)
- `.crt`: public certificate (signed by the CA)
- `.pem`: container using base64, e.g.
  ```text
  -----BEGIN FOO-----
  <base64 encoded data>
  -----END FOO-----
  ```
  <span>
- `.p12`: PKCS12 file that combines private key and certificate


### Best practices

- File extensions don't matter (mostly)
  - People tend to mix stuff, e.g. `private.key`, `privkey.pem`, ...
- Use descriptive filenames
  - Recommendation: `<name>.<type>.<container>`, e.g. `web-1.key.pem`, `web-1.crt.pem`

---

## Generating a private key

- `openssl genrsa` -> 2048 bit RSA
  - most basic command
  - should be increased to 3072 bits
  - 'Good old' RSA key -> it's good but **old**

<!-- stop -->
### More modern approach: Elliptic curve


| RSA and DH Key Size (bits) | Elliptic Curve Key Size (bits) |
|:-----:|:---:|
|  1024 | 160 |
|  2048 | 224 |
|  3072 | 256 |
|  7680 | 384 |
| 15360 | 521 |

> NIST Recommended Key Sizes [^1]
---

- `openssl ecparam -genkey -name <name>` -> Elliptic Curve with variety of keys
  - `openssl ecparam -list_curves` to see all of them
- `openssl genpkey` -> Even more algorithms to choose
  - Using `openssl genpkey -algorithm ed25519 -out certs/ca.key.pem` for this presentation
  - non-NIST curve without "nasty prime formation" [^2]
```terminal16
bash -il
```

---

## Generating a CA certificate

```bash
openssl req -new -x509 -nodes -days $((10*365)) \
   -subj "/CN=ca.my.domain" \
   -key certs/ca.key.pem \
   -out certs/ca.crt.pem
```

```terminal8
bash -il
```

---

## Sources

[^1] https://web.archive.org/web/20090117023500/http://www.nsa.gov/business/programs/elliptic_curve.shtml

[^2] https://blog.cr.yp.to/20140323-ecdsa.html
