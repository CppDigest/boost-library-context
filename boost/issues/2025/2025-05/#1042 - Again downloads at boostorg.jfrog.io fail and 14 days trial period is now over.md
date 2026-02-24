# #1042 - Again downloads at boostorg.jfrog.io fail and 14 days trial period is now over [Closed]

> Username: 0xFEEDC0DE64  
> Created at: 2025-05-26 17:09:41 UTC  
> Updated at: 2025-05-26 17:13:54 UTC  
> Closed at: 2025-05-26 17:13:54 UTC  
> Url: https://github.com/boostorg/boost/issues/1042  

boostorg.jfrog.io is one of the most unreliable services hosted in the internet, I know.  
  
is there anything we can do to get it up and running again?  
  
buildroot builds checking out boost fail now because of failing checksums,  
curl shows a random redirect to a page telling us "14 days trial period is now over".  
  
Can boost please not host on free trial servers and maybe get some reliable hosting somewhere?  
  
same problem as back then with #849 and #842  
  
```  
$ curl -v https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source  
* Host boostorg.jfrog.io:443 was resolved.  
* IPv6: (none)  
* IPv4: 3.95.117.170, 18.214.194.113, 18.232.172.199  
*   Trying 3.95.117.170:443...  
* ALPN: curl offers h2,http/1.1  
* TLSv1.3 (OUT), TLS handshake, Client hello (1):  
*  CAfile: /etc/ssl/certs/ca-certificates.crt  
*  CApath: none  
* TLSv1.3 (IN), TLS handshake, Server hello (2):  
* TLSv1.2 (IN), TLS handshake, Certificate (11):  
* TLSv1.2 (IN), TLS handshake, Server key exchange (12):  
* TLSv1.2 (IN), TLS handshake, Server finished (14):  
* TLSv1.2 (OUT), TLS handshake, Client key exchange (16):  
* TLSv1.2 (OUT), TLS change cipher, Change cipher spec (1):  
* TLSv1.2 (OUT), TLS handshake, Finished (20):  
* TLSv1.2 (IN), TLS handshake, Finished (20):  
* SSL connection using TLSv1.2 / ECDHE-RSA-AES256-GCM-SHA384 / x25519 / RSASSA-PSS  
* ALPN: server accepted http/1.1  
* Server certificate:  
*  subject: CN=*.jfrog.io  
*  start date: Jan 19 00:00:00 2025 GMT  
*  expire date: Feb 19 23:59:59 2026 GMT  
*  subjectAltName: host "boostorg.jfrog.io" matched cert's "*.jfrog.io"  
*  issuer: C=US; O=DigiCert Inc; OU=www.digicert.com; CN=GeoTrust TLS RSA CA G1  
*  SSL certificate verify ok.  
*   Certificate level 0: Public key type RSA (2048/112 Bits/secBits), signed using sha256WithRSAEncryption  
*   Certificate level 1: Public key type RSA (2048/112 Bits/secBits), signed using sha256WithRSAEncryption  
*   Certificate level 2: Public key type RSA (2048/112 Bits/secBits), signed using sha256WithRSAEncryption  
* Connected to boostorg.jfrog.io (3.95.117.170) port 443  
* using HTTP/1.x  
> GET /artifactory/main/release/1.83.0/source HTTP/1.1  
> Host: boostorg.jfrog.io  
> User-Agent: curl/8.13.0  
> Accept: */*  
>   
* Request completely sent off  
< HTTP/1.1 302 Moved Temporarily  
< Date: Mon, 26 May 2025 17:06:18 GMT  
< Content-Type: text/html  
< Content-Length: 138  
< Connection: keep-alive  
< Location: https://landing.jfrog.com/reactivate-server/boostorg  
<   
<html>  
<head><title>302 Found</title></head>  
<body>  
<center><h1>302 Found</h1></center>  
<hr><center>nginx</center>  
</body>  
</html>  
* Connection #0 to host boostorg.jfrog.io left intact  
```

---

## Comment 1

> Username: mclow  
> Created at: 2025-05-26 17:13:54 UTC  
> Url: https://github.com/boostorg/boost/issues/1042#issuecomment-2910296589  

Have you noticed that we have switched providers? (almost a year ago)  
  
The link you want is https://archives.boost.io/release/1.83.0/source/
