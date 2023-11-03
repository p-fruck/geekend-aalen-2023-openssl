# Geekend Aalen 2023 - Introduction to OpenSSL

This repository contains the presentation held at the [2023 Aalen Geekend](https://media.ccc.de/b/erfas/hackwerk-aalen/geekend-2023).

## Presentation

The serve the presentation, have a look at the `slides.md` in raw form or use the `lookatme` terminal presentation tool. This tool can be installed using

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
# lookatme requires rather specific versions to function properly...
pip install lookatme==3.0.0rc5 --no-deps
```

The slides can be rendered using `lookatme slides.md -e terminal`

## Learn more

For cryptographic recommendations, the NIST and BSI are the main sources I used and I can recommend. Here are some example links in case you want to read more about this topic:
- [NIST: Cryptographic Standards and Guidelines](https://csrc.nist.gov/projects/cryptographic-standards-and-guidelines)
- [NIST: Transitioning the Use of Cryptographic Algorithms and Key Lengths](https://csrc.nist.gov/pubs/sp/800/131/a/r2/final)
- (German) [BSI TR-02102 Kryptographische Verfahren: Empfehlungen und Schlüssellänge](https://www.bsi.bund.de/DE/Themen/Unternehmen-und-Organisationen/Standards-und-Zertifizierung/Technische-Richtlinien/TR-nach-Thema-sortiert/tr02102/tr02102_node.html)
- Or use https://www.keylength.com/en/ to get a quick overview of multiple recommendations of different institutions

## Generating a private key

Generating the private key is likely the most important step when generating a new certificate, since the key size represents the security of the certificate. For that reason, this step is explained explicitly. All other information should be also accessible from the slides.

In order to generate a new private key for your certificates the command `openssl genrsa` is likely used the most. This simple command will generate a good old 2048 bit RSA private key. This key size can be adapted to be more future proof. By running `openssl genrsa 3072` the key size is increased to 3072 which equals the default length of an RSA key created by the `ssh-keygen` command. The private key is then simply printed to the terminal. To store the private key without printing, you can either append the `-out privkey.pem` parameters or just pipe it to a file using `openssl genrsa > privkey.pem`.

Of course, longer keys do require more computational effort. In the case of RSA the term 'good old' matches fairly well. While it is a well established and understood standard that is secure (if implemented properly) it is also fairly old. A more modern standard in the cryptographic world is the use of Elliptic Curve (EC) keys. According to the NSA[^1] an RSA key size of 2048 compares to an EC key size of 224. When aiming for really large (and therefore rather impractical) key sizes, an RSA key size of 15360 compares to an EC key size of 521.

To use other algorithms as RSA the commands `openssl genpkey` or `openssl ecparam -genkey` can be used. Those commands allows to generate private keys by specifying the algorithm manually. For the `genpkey` commnd RSA, RSA‐PSS, EC, X25519, X448, ED25519 and ED448 are valid values[^2]. The `ecparam -genkey` command on the other hand provides access to secp, prime and brainpool curves[^3].

The time required to generate a new private key depends on your CPU and current system entropy. On my mid-range system, an `openssl genrsa 15360` command takes over two minutes and generates a large key compared to `openssl ecparam -genkey -name <secp521r1|brainpoolP512t1>` which execute almost instantaniously and only return a couple of lines for the private key.

While those 521 bit curves look very promising, I personally trust in ed25519 which is a non-NIST curve designed to make implementation (in constant time) more simple than ECDSA with NIST P-256 as the default curve. An ed25519 key can be generated using `openssl genpkey -algorithm ed25519`[^4].

[^1] https://web.archive.org/web/20090117023500/http://www.nsa.gov/business/programs/elliptic_curve.shtml
[^2] `man openssl-genpkey`
[^3] `openssl ecparam -list_curves`
[^4] https://blog.cr.yp.to/20140323-ecdsa.html
