
```bash
Please enter your selection [0-20]: 19
	1. Get SSL
	2. Revoke
	3. Force Renew
	4. Self-signed Certificate
Choose an option: 3
Please enter your domain name to forcefully renew an SSL certificate: s-sub.shensky.shop
[Mon Jan 12 06:34:56 UTC 2026] The domain 's-sub.shensky.shop' seems to already have an ECC cert, let's use it.
[Mon Jan 12 06:34:56 UTC 2026] Renewing: 's-sub.shensky.shop'
[Mon Jan 12 06:34:56 UTC 2026] Renewing using Le_API=https://acme-v02.api.letsencrypt.org/directory
[Mon Jan 12 06:34:56 UTC 2026] Using CA: https://acme-v02.api.letsencrypt.org/directory
[Mon Jan 12 06:34:56 UTC 2026] Standalone mode.
[Mon Jan 12 06:34:56 UTC 2026] Single domain='s-sub.shensky.shop'
[Mon Jan 12 06:34:58 UTC 2026] Getting webroot for domain='s-sub.shensky.shop'
[Mon Jan 12 06:34:58 UTC 2026] Verifying: s-sub.shensky.shop
[Mon Jan 12 06:34:58 UTC 2026] Standalone mode server
[Mon Jan 12 06:34:59 UTC 2026] Pending. The CA is processing your order, please wait. (1/30)
[Mon Jan 12 06:35:03 UTC 2026] Pending. The CA is processing your order, please wait. (2/30)
[Mon Jan 12 06:35:06 UTC 2026] Success
[Mon Jan 12 06:35:06 UTC 2026] Verification finished, beginning signing.
[Mon Jan 12 06:35:06 UTC 2026] Let's finalize the order.
[Mon Jan 12 06:35:06 UTC 2026] Le_OrderFinalize='https://acme-v02.api.letsencrypt.org/acme/finalize/2956335586/468634524686'
[Mon Jan 12 06:35:08 UTC 2026] Downloading cert.
[Mon Jan 12 06:35:08 UTC 2026] Le_LinkCert='https://acme-v02.api.letsencrypt.org/acme/cert/05af6b99b1e3768f36362021d3d2d8436917'
[Mon Jan 12 06:35:08 UTC 2026] Cert success.
-----BEGIN CERTIFICATE-----
MIIDmTCCAyCgAwIBAgISBa9rmbHjdo82NiAh09LYQ2kXMAoGCCqGSM49BAMDMDIx
CzAJBgNVBAYTAlVTMRYwFAYDVQQKEw1MZXQncyBFbmNyeXB0MQswCQYDVQQDEwJF
ODAeFw0yNjAxMTIwNTM2MzZaFw0yNjA0MTIwNTM2MzVaMB0xGzAZBgNVBAMTEnMt
c3ViLnNoZW5za3kuc2hvcDBZMBMGByqGSM49AgEGCCqGSM49AwEHA0IABA24yEHt
OpDBwLXWzTELRpVwT2bYDTxlG5IwkAKppu/sJrPl5f9UJJ6xfLdxtuG5dXllBUlL
KaJcfQu7KcalMTGjggIpMIICJTAOBgNVHQ8BAf8EBAMCB4AwHQYDVR0lBBYwFAYI
KwYBBQUHAwEGCCsGAQUFBwMCMAwGA1UdEwEB/wQCMAAwHQYDVR0OBBYEFKQ2B2qy
BIrafrc2xFVbTYSMe3dRMB8GA1UdIwQYMBaAFI8NE6L2Ln7RUGwzGDhdWY4jcpHK
MDIGCCsGAQUFBwEBBCYwJDAiBggrBgEFBQcwAoYWaHR0cDovL2U4LmkubGVuY3Iu
b3JnLzAdBgNVHREEFjAUghJzLXN1Yi5zaGVuc2t5LnNob3AwEwYDVR0gBAwwCjAI
BgZngQwBAgEwLQYDVR0fBCYwJDAioCCgHoYcaHR0cDovL2U4LmMubGVuY3Iub3Jn
LzI0LmNybDCCAQ0GCisGAQQB1nkCBAIEgf4EgfsA+QB3AGQRxGykEuyniRyiAi4A
vKtPKAfUHjUnq+r+1QPJfc3wAAABm7DqWSsAAAQDAEgwRgIhAPiZ+hRUsVL8kZHe
M8W8bzT17w6CuCd9HIkhbpG2AWjYAiEArYjbs59jqv6YdVdZc8e9xs5dFLDbS7/L
oxxuyD3sEAYAfgAai51pSleYyJmgyoi99I/AtFZgzMNgDR9x9Gn/x9GsowAAAZuw
6l1DAAgAAAUANYQzfgQDAEcwRQIhALPPN/GCeWPg38gMP/hTMPjQChlUrJpxxgbD
gKTJc5iBAiBGslxZTn4o2YvWvjYPiqxQt5X3k7XTBYKOI2DENYKnJjAKBggqhkjO
PQQDAwNnADBkAjBLFLqhSsOzjZgf77ulk8CeHUptWBghPMroJXDfKTol+kovJBXY
MAVItDqDV/O4tQkCMCvWTevH67Dm35P1/+MQnxKitE/hSHTqY+NUcX15a6By7Ok+
rsDnrkWeg8A5lKqsuA==
-----END CERTIFICATE-----
[Mon Jan 12 06:35:08 UTC 2026] Your cert is in: /root/.acme.sh/s-sub.shensky.shop_ecc/s-sub.shensky.shop.cer
[Mon Jan 12 06:35:08 UTC 2026] Your cert key is in: /root/.acme.sh/s-sub.shensky.shop_ecc/s-sub.shensky.shop.key
[Mon Jan 12 06:35:08 UTC 2026] The intermediate CA cert is in: /root/.acme.sh/s-sub.shensky.shop_ecc/ca.cer
[Mon Jan 12 06:35:08 UTC 2026] And the full-chain cert is in: /root/.acme.sh/s-sub.shensky.shop_ecc/fullchain.cer
```