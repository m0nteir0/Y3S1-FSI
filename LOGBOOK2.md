# [CVE-2022-0778](https://www.cvedetails.com/cve/CVE-2022-0778/)

## IDENTIFICATION

- The BN_mod_sqrt() function, which computes a modular square root, contains a bug that can cause it to loop forever for non-prime moduli.
- Internally this function is used when parsing certificates that contain elliptic curve public keys in compressed form or explicit elliptic curve parameters with a base point encoded in compressed form.
- It affects the OpenSSL versions 1.0.2, 1.1.1 and 3.0 and, by consequence, many technologies that use OpenSSL (e.g. Debian Linux, MariaDB, Node.js, etc.).
- This vulnerability lies on the Tonelli-Shanks algorithm, which is used to compute the modular square root.

## CATALOGUING

- Tavis Ormandy from Google Project Zero team fixed the BN_mod_sqrt() function of OpenSSL that could be tricked into an infinite loop.
- Published on 2022-03-15 by OpenSSL Software Foundation.
- Exploitability: 10.0 in CVEdetails.com - This vulnerability is easy to exploit.
- This vulnerability allows an attacker to cause a denial of service (DoS) attack.

## EXPLOIT

- It is possible to trigger the infinite loop by crafting a certificate that has invalid explicit curve parameters.
- These parameters are pairs of numbers (a, p) such as (696,697). P must be a prime number for the algorithm to work correctly, but there was no code that checked it. Thus, these invalid parameters were accepted and caused the infinite loop.
- Instructions to replicate this exploit can be found in [this repository](https://github.com/drago-96/CVE-2022-0778)

## ATTACKS

- As OpenSSL is a widely used software library for TLS and SSL protocols, this vulnerability propagates to many technologies that use it (as mentioned above).
- As this vulnerability was only published in the 15th of March of 2022, and the commit that fixes it was made at the 28th of February of 2022 (and merged the same day this CVE was published), there are no known attacks that exploit this vulnerability. [Source](https://git.openssl.org/gitweb/?p=openssl.git;a=commitdiff;h=a466912611aa6cbdf550cd10601390e587451246)
- The vulnerability could affect both clients and servers.
