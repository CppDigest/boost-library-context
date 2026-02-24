# #1467 - Beast fails to read request headers on low MTU [Closed]

> Username: Al2Klimov  
> Created at: 2019-02-21 15:11:26 UTC  
> Updated at: 2019-02-22 09:11:56 UTC  
> Closed at: 2019-02-21 17:31:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1467  

### Version of Beast  
  
The one included in Boost v1.69.0.  
  
### Steps necessary to reproduce the problem  
  
https://github.com/Al2Klimov/poc-beast-bytewise  
  
### All relevant compiler information  
  
GCC 6.3  
  
### Commentary  
  
As (the employee of) a "commercial" user **I could spend some time with fixing this**... if I'd at least know where to start. (Really, it was hard not to jump out of the window while migrating to ASIO.)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-21 15:12:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466036239  

Phew that is quite a complete defect report!

---

## Comment 2

> Username: Al2Klimov  
> Created at: 2019-02-21 15:38:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466046342  

Sure. As the responsible one for the ASIO migration of **one of our key apps** I have to ensure that the app works w/o any tradeoffs. 🔍

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-02-21 16:11:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466059456  

I installed Docker Desktop for Windows and it crashes my computer on startup, trying to recover now...

---

## Comment 4

> Username: Al2Klimov  
> Created at: 2019-02-21 16:30:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466067400  

You don't even need Docker, just the Beast usage example with one thread.

---

## Comment 5

> Username: Al2Klimov  
> Created at: 2019-02-21 16:38:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466070326  

I just Dockerize everything to make spinning up PoCs as easy as possible on any platform – especially if there are a lot of dependencies. But in this case not using Docker turned out to be easier...

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-02-21 16:38:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466070467  

But I thought i had to lower my MTU or something?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-02-21 16:38:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466070604  

Is your example modified or is it the stock example?

---

## Comment 8

> Username: Al2Klimov  
> Created at: 2019-02-21 16:41:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466071562  

The Dockerized app is the copy&pasted file linked from the readme. The low MTU is emulated on the client side by sending one byte every 0.001s (see the Perl+OpenSSL command).

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-02-21 16:44:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466073217  

First command works great for me:  
```  
vinnie@VF-Corsair MINGW64 /  
$ perl -e 'sleep 2; for (;;) { for my $c (split("", "GET /v1/status HTTP/1.1\r\nAuthorization: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3\r\nContent-Length: 0\r\n\r\n")) { print STDERR $c; print $c; $| = 1; select(undef, undef, undef, 0.01); } }; sleep 3600' |openssl s_client -connect 127.0.0.1:8443  
CONNECTED(0000011C)  
---  
Certificate chain  
 0 s:C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
   i:C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
---  
Server certificate  
-----BEGIN CERTIFICATE-----  
MIIDaDCCAlCgAwIBAgIJAO8vBu8i8exWMA0GCSqGSIb3DQEBCwUAMEkxCzAJBgNV  
BAYTAlVTMQswCQYDVQQIDAJDQTEtMCsGA1UEBwwkTG9zIEFuZ2VsZXNPPUJlYXN0  
Q049d3d3LmV4YW1wbGUuY29tMB4XDTE3MDUwMzE4MzkxMloXDTQ0MDkxODE4Mzkx  
MlowSTELMAkGA1UEBhMCVVMxCzAJBgNVBAgMAkNBMS0wKwYDVQQHDCRMb3MgQW5n  
ZWxlc089QmVhc3RDTj13d3cuZXhhbXBsZS5jb20wggEiMA0GCSqGSIb3DQEBAQUA  
A4IBDwAwggEKAoIBAQDJ7BRKFO8fqmsEXw8v9YOVXyrQVsVbjSSGEs4Vzs4cJgcF  
xqGitbnLIrOgiJpRAPLy5MNcAXE1strVGfdEf7xMYSZ/4wOrxUyVw/Ltgsft8m7b  
Fu8TsCzO6XrxpnVtWk506YZ7ToTa5UjHfBi2+pWTxbpN12UhiZNUcrRsqTFW+6fO  
9d7xm5wlaZG8cMdg0cO1bhkz45JSl3wWKIES7t3EfKePZbNlQ5hPy7Pd5JTmdGBp  
yY8anC8u4LPbmgW0/U31PH0rRVfGcBbZsAoQw5Tc5dnb6N2GEIbq3ehSfdDHGnrv  
enu2tOK9Qx6GEzXh3sekZkxcgh+NlIxCNxu//Dk9AgMBAAGjUzBRMB0GA1UdDgQW  
BBTZh0N9Ne1OD7GBGJYz4PNESHuXezAfBgNVHSMEGDAWgBTZh0N9Ne1OD7GBGJYz  
4PNESHuXezAPBgNVHRMBAf8EBTADAQH/MA0GCSqGSIb3DQEBCwUAA4IBAQCmTJVT  
LH5Cru1vXtzb3N9dyolcVH82xFVwPewArchgq+CEkajOU9bnzCqvhM4CryBb4cUs  
gqXWp85hAh55uBOqXb2yyESEleMCJEiVTwm/m26FdONvEGptsiCmF5Gxi0YRtn8N  
V+KhrQaAyLrLdPYI7TrwAOisq2I1cD0mt+xgwuv/654Rl3IhOMx+fKWKJ9qLAiaE  
fQyshjlPP9mYVxWOxqctUdQ8UnsUKKGEUcVrA08i1OAnVKlPFjKBvk+r7jpsTPcr  
9pWXTO9JrYMML7d+XRSZA1n3856OqZDX4403+9FnXCvfcLZLLKTBvwwFgEFGpzjK  
UEVbkhd5qstF6qWK  
-----END CERTIFICATE-----  
subject=C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
  
issuer=C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
  
---  
No client certificate CA names sent  
Peer signing digest: SHA256  
Peer signature type: RSA  
Server Temp Key: DH, 2048 bits  
---  
SSL handshake has read 1988 bytes and written 629 bytes  
Verification error: self signed certificate  
---  
New, TLSv1.2, Cipher is DHE-RSA-AES256-GCM-SHA384  
Server public key is 2048 bit  
Secure Renegotiation IS supported  
Compression: NONE  
Expansion: NONE  
No ALPN negotiated  
SSL-Session:  
    Protocol  : TLSv1.2  
    Cipher    : DHE-RSA-AES256-GCM-SHA384  
    Session-ID: 80A8D234CE80E28D5BC333B8EEC34A86D682814381F882CB141FD9ED4C74C9B1  
    Session-ID-ctx:  
    Master-Key: 98227A9F8DC96EE0859381C865A73CE981664A5C5C6A153FC1C14BBF6CE827BC58B95BA620DA94F67FAF82A958E8E4B7  
    PSK identity: None  
    PSK identity hint: None  
    SRP username: None  
    TLS session ticket lifetime hint: 300 (seconds)  
    TLS session ticket:  
    0000 - 20 de 96 8e b7 c8 a4 cf-97 6c 51 9b 4a 89 10 91    ........lQ.J...  
    0010 - fc 08 3f 18 8a db 9c 49-c8 4d da 69 ef 4f 08 68   ..?....I.M.i.O.h  
    0020 - 40 f2 0f 07 4f d7 cd 14-16 40 4a 99 92 76 19 04   @...O....@J..v..  
    0030 - d2 e1 1e 53 aa 12 1e 57-3d 29 f7 a7 68 af 31 1a   ...S...W=)..h.1.  
    0040 - 1b 64 b5 dc cb bd 6a cf-4c 48 30 18 74 da 4f a8   .d....j.LH0.t.O.  
    0050 - 19 1a 38 05 c2 fc 88 38-84 06 97 a6 d4 2b cd b1   ..8....8.....+..  
    0060 - e7 43 4a 93 eb 9b ae e9-cd 5b d8 cc 07 fd 28 c2   .CJ......[....(.  
    0070 - 0c 07 04 67 3a cd f4 a3-f7 83 e7 4b 1e 12 07 26   ...g:......K...&  
    0080 - e2 59 e3 f4 60 c7 20 08-ec 22 4d 04 a6 bf b7 cc   .Y..`. .."M.....  
    0090 - 86 06 41 dd af 5e 7f 07-2f 71 fc 55 65 60 60 6b   ..A..^../q.Ue``k  
    00a0 - 05 f4 2d 13 54 fb 07 55-14 6b 29 eb bf cd a6 31   ..-.T..U.k)....1  
  
    Start Time: 1550767351  
    Timeout   : 7200 (sec)  
    Verify return code: 18 (self signed certificate)  
    Extended master secret: no  
---  
GET /v1/status HTTP/1.1  
Authorization: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
AuthorizatiHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.on: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GEHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.T /v1/status HTTP/1.1  
Authorization: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
AuthorizHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.ation: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GEHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.T /v1/status HTTP/1.1  
Authorization: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
AuthorizatioHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.n: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GET /HTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.v1/status HTTP/1.1  
Authorization: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
Authorization: BHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.asic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GET /v1/sHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.tatus HTTP/1.1  
Authorization: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
Authorization: Basic cmHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GET /v1/status HHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.TTP/1.1  
Authorization: depth=0 C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
verify error:num=18:self signed certificate  
verify return:1  
depth=0 C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
verify return:1  
  
  
vinnie@VF-Corsair MINGW64 /  
$  
```

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-02-21 16:45:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466073469  

> The low MTU is emulated on the client side by sending one byte every 0.001s  
  
OH That explains why the messages were being printed so slowly... LOL... I thought CMD.EXE output buffer filled up or something.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-02-21 16:47:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466074148  

Second command also works:  
```  
vinnie@VF-Corsair MINGW64 /  
$ perl -e 'sleep 2; for (;;) { for my $c (split("", "GET /v1/status HTTP/1.1\r\nAuthorization: Basic AAAA\r\nContent-Length: 0\r\n\r\n")) { print STDERR $c; print $c; $| = 1; select(undef, undef, undef, 0.01); } }; sleep 3600' |openssl s_client -connect 127.0.0.1:8443  
CONNECTED(000000F8)  
---  
Certificate chain  
 0 s:C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
   i:C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
---  
Server certificate  
-----BEGIN CERTIFICATE-----  
MIIDaDCCAlCgAwIBAgIJAO8vBu8i8exWMA0GCSqGSIb3DQEBCwUAMEkxCzAJBgNV  
BAYTAlVTMQswCQYDVQQIDAJDQTEtMCsGA1UEBwwkTG9zIEFuZ2VsZXNPPUJlYXN0  
Q049d3d3LmV4YW1wbGUuY29tMB4XDTE3MDUwMzE4MzkxMloXDTQ0MDkxODE4Mzkx  
MlowSTELMAkGA1UEBhMCVVMxCzAJBgNVBAgMAkNBMS0wKwYDVQQHDCRMb3MgQW5n  
ZWxlc089QmVhc3RDTj13d3cuZXhhbXBsZS5jb20wggEiMA0GCSqGSIb3DQEBAQUA  
A4IBDwAwggEKAoIBAQDJ7BRKFO8fqmsEXw8v9YOVXyrQVsVbjSSGEs4Vzs4cJgcF  
xqGitbnLIrOgiJpRAPLy5MNcAXE1strVGfdEf7xMYSZ/4wOrxUyVw/Ltgsft8m7b  
Fu8TsCzO6XrxpnVtWk506YZ7ToTa5UjHfBi2+pWTxbpN12UhiZNUcrRsqTFW+6fO  
9d7xm5wlaZG8cMdg0cO1bhkz45JSl3wWKIES7t3EfKePZbNlQ5hPy7Pd5JTmdGBp  
yY8anC8u4LPbmgW0/U31PH0rRVfGcBbZsAoQw5Tc5dnb6N2GEIbq3ehSfdDHGnrv  
enu2tOK9Qx6GEzXh3sekZkxcgh+NlIxCNxu//Dk9AgMBAAGjUzBRMB0GA1UdDgQW  
BBTZh0N9Ne1OD7GBGJYz4PNESHuXezAfBgNVHSMEGDAWgBTZh0N9Ne1OD7GBGJYz  
4PNESHuXezAPBgNVHRMBAf8EBTADAQH/MA0GCSqGSIb3DQEBCwUAA4IBAQCmTJVT  
LH5Cru1vXtzb3N9dyolcVH82xFVwPewArchgq+CEkajOU9bnzCqvhM4CryBb4cUs  
gqXWp85hAh55uBOqXb2yyESEleMCJEiVTwm/m26FdONvEGptsiCmF5Gxi0YRtn8N  
V+KhrQaAyLrLdPYI7TrwAOisq2I1cD0mt+xgwuv/654Rl3IhOMx+fKWKJ9qLAiaE  
fQyshjlPP9mYVxWOxqctUdQ8UnsUKKGEUcVrA08i1OAnVKlPFjKBvk+r7jpsTPcr  
9pWXTO9JrYMML7d+XRSZA1n3856OqZDX4403+9FnXCvfcLZLLKTBvwwFgEFGpzjK  
UEVbkhd5qstF6qWK  
-----END CERTIFICATE-----  
subject=C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
  
issuer=C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
  
---  
No client certificate CA names sent  
Peer signing digest: SHA256  
Peer signature type: RSA  
Server Temp Key: DH, 2048 bits  
---  
SSL handshake has read 1988 bytes and written 629 bytes  
Verification error: self signed certificate  
---  
New, TLSv1.2, Cipher is DHE-RSA-AES256-GCM-SHA384  
Server public key is 2048 bit  
Secure Renegotiation IS supported  
Compression: NONE  
Expansion: NONE  
No ALPN negotiated  
SSL-Session:  
    Protocol  : TLSv1.2  
    Cipher    : DHE-RSA-AES256-GCM-SHA384  
    Session-ID: F7C27D5533B4F85430980E03D07308F09AFAA3F3742B1046CEA191713F3A9B44  
    Session-ID-ctx:  
    Master-Key: ED2AA4B2F02BB047C99FD7FCE188DE3DE59D65599AAD2349C5E753BD89BD23EA015173427753D8414D539C4212B54642  
    PSK identity: None  
    PSK identity hint: None  
    SRP username: None  
    TLS session ticket lifetime hint: 300 (seconds)  
    TLS session ticket:  
    0000 - 20 de 96 8e b7 c8 a4 cf-97 6c 51 9b 4a 89 10 91    ........lQ.J...  
    0010 - c3 ba 87 02 6f 15 32 aa-1c 7c 73 0d 83 3d c2 b6   ....o.2..|s..=..  
    0020 - b9 7a 2f b2 9e 40 ff d0-09 da c6 de 5a d5 5f 76   .z/..@......Z._v  
    0030 - 19 65 83 b9 37 62 57 bc-f7 c2 07 5b 69 57 4e 3b   .e..7bW....[iWN;  
    0040 - 83 65 77 a9 86 1a 73 56-38 41 19 ce 0c 3b e3 4e   .ew...sV8A...;.N  
    0050 - 91 6d 41 be a2 5a f3 5b-60 64 30 e8 34 d5 8b 70   .mA..Z.[`d0.4..p  
    0060 - 64 65 17 38 86 29 4f e7-17 f1 05 79 e0 a6 48 56   de.8.)O....y..HV  
    0070 - 56 08 46 ea ef b1 1e 97-34 99 d5 53 e7 84 30 73   V.F.....4..S..0s  
    0080 - d3 c8 70 58 ff ee f7 93-51 a6 05 93 89 bc a4 0e   ..pX....Q.......  
    0090 - 6e a7 f8 d9 6b 17 47 e2-59 5a d6 66 0a 95 88 ae   n...k.G.YZ.f....  
    00a0 - ae 68 0e 1f fa 4c 9e 2f-b4 79 02 77 28 be 85 36   .h...L./.y.w(..6  
  
    Start Time: 1550767587  
    Timeout   : 7200 (sec)  
    Verify return code: 18 (self signed certificate)  
    Extended master secret: no  
---  
GET /v1/status HTTP/1.1  
Authorization: Basic AAAA  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
Authorization: Basic AAAA  
ContenHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.t-Length: 0  
  
GET /v1/status HTTP/1.1  
Authorization: Basic AAAHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.A  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
Authorization: BasHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.ic AAAA  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
AuthorizaHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.tion: Basic AAAA  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
AHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.uthorization: Basic AAAA  
Content-Length: 0  
  
GET /v1/status HTTPHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found./1.1  
Authorization: Basic AAAA  
Content-Length: 0  
  
GET /v1/stHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.atus HTTP/1.1  
Authorization: Basic AAAA  
Content-Length: 0  
  
GHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.ET /v1/status HTTP/1.1  
Authorization: Basic AAAA  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
Authorization: Basic AAAA  
ConteHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.nt-Length: 0  
  
GET /v1/status HTTP/1.1  
Authorization: Basic AAAHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.A  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
Authorization: BHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.asic AAAA  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
AuthoriHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.zation: Basic AAAA  
Content-Length: 0  
  
HTTP/1.1 404 Not Found1  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.  
Authorization: Basic AAAA  
Content-Length: 0  
  
GET /v1/status HTHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.TP/1.1  
Authorization: Basic AAAA  
Content-Length: 0  
  
GET /v1/HTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.status HTTP/1.1  
Authorization: Basic AAAA  
Content-Length: 0  
  
HTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.GET /v1/status HTTP/1.1  
Authorization: Basic AAAA  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
Authorization: Basic AAAA  
Content-Length: 0  
  
GEHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.HTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.T /v1/status HTTP/1.1  
Authorization: Basic AAAA  
Content-Length: 0  
  
GET /v1/status HTTP/HTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.1.1  
Authorization: Basic AAAA  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
AuthorizationHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.: Basic AAAA  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
Authorization: Basic AAAA  
ContHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.ent-Length: 0  
  
GET /v1/status HTdepth=0 C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
verify error:num=18:self signed certificate  
verify return:1  
depth=0 C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
verify return:1  
  
  
vinnie@VF-Corsair MINGW64 /  
$  
```

---

## Comment 12

> Username: Al2Klimov  
> Created at: 2019-02-21 16:48:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466074682  

For me the 1st one causes the server to print "read: partial message".

---

## Comment 13

> Username: vinniefalco  
> Created at: 2019-02-21 16:49:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466075163  

I am **extremely skeptical** that this is a problem in beast, or even that you will be able to find a bug, because this code is very well tested, and currently being used in production servers exposed to hostile and malicious peers 24/7.  
  
HTTP stream read tests:  
https://github.com/boostorg/beast/blob/develop/test/beast/http/read.cpp  
  
HTTP parser tests, note how all permutations of fragmented inputs are tested:  
https://github.com/boostorg/beast/blob/28f5921bd613d5076d640d094926a684731799c3/test/beast/http/parser.cpp#L88

---

## Comment 14

> Username: vinniefalco  
> Created at: 2019-02-21 16:50:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466075592  

> For me the 1st one causes the server to print "read: partial message".  
  
Ah! That means that the client closed the connection before it finished sending the HTTP message:  
https://github.com/boostorg/beast/blob/28f5921bd613d5076d640d094926a684731799c3/include/boost/beast/http/impl/basic_parser.ipp#L276

---

## Comment 15

> Username: Al2Klimov  
> Created at: 2019-02-21 16:52:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466076363  

As far as I understand the client (I've written it) it won't close the connection any way.

---

## Comment 16

> Username: vinniefalco  
> Created at: 2019-02-21 16:53:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466076499  

Output of first command with a **release** build  
```  
vinnie@VF-Corsair MINGW64 /  
$ perl -e 'sleep 2; for (;;) { for my $c (split("", "GET /v1/status HTTP/1.1\r\nAuthorization: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3\r\nContent-Length: 0\r\n\r\n")) { print STDERR $c; print $c; $| = 1; select(undef, undef, undef, 0.01); } }; sleep 3600' |openssl s_client -connect 127.0.0.1:8443  
CONNECTED(000000F8)  
---  
Certificate chain  
 0 s:C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
   i:C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
---  
Server certificate  
-----BEGIN CERTIFICATE-----  
MIIDaDCCAlCgAwIBAgIJAO8vBu8i8exWMA0GCSqGSIb3DQEBCwUAMEkxCzAJBgNV  
BAYTAlVTMQswCQYDVQQIDAJDQTEtMCsGA1UEBwwkTG9zIEFuZ2VsZXNPPUJlYXN0  
Q049d3d3LmV4YW1wbGUuY29tMB4XDTE3MDUwMzE4MzkxMloXDTQ0MDkxODE4Mzkx  
MlowSTELMAkGA1UEBhMCVVMxCzAJBgNVBAgMAkNBMS0wKwYDVQQHDCRMb3MgQW5n  
ZWxlc089QmVhc3RDTj13d3cuZXhhbXBsZS5jb20wggEiMA0GCSqGSIb3DQEBAQUA  
A4IBDwAwggEKAoIBAQDJ7BRKFO8fqmsEXw8v9YOVXyrQVsVbjSSGEs4Vzs4cJgcF  
xqGitbnLIrOgiJpRAPLy5MNcAXE1strVGfdEf7xMYSZ/4wOrxUyVw/Ltgsft8m7b  
Fu8TsCzO6XrxpnVtWk506YZ7ToTa5UjHfBi2+pWTxbpN12UhiZNUcrRsqTFW+6fO  
9d7xm5wlaZG8cMdg0cO1bhkz45JSl3wWKIES7t3EfKePZbNlQ5hPy7Pd5JTmdGBp  
yY8anC8u4LPbmgW0/U31PH0rRVfGcBbZsAoQw5Tc5dnb6N2GEIbq3ehSfdDHGnrv  
enu2tOK9Qx6GEzXh3sekZkxcgh+NlIxCNxu//Dk9AgMBAAGjUzBRMB0GA1UdDgQW  
BBTZh0N9Ne1OD7GBGJYz4PNESHuXezAfBgNVHSMEGDAWgBTZh0N9Ne1OD7GBGJYz  
4PNESHuXezAPBgNVHRMBAf8EBTADAQH/MA0GCSqGSIb3DQEBCwUAA4IBAQCmTJVT  
LH5Cru1vXtzb3N9dyolcVH82xFVwPewArchgq+CEkajOU9bnzCqvhM4CryBb4cUs  
gqXWp85hAh55uBOqXb2yyESEleMCJEiVTwm/m26FdONvEGptsiCmF5Gxi0YRtn8N  
V+KhrQaAyLrLdPYI7TrwAOisq2I1cD0mt+xgwuv/654Rl3IhOMx+fKWKJ9qLAiaE  
fQyshjlPP9mYVxWOxqctUdQ8UnsUKKGEUcVrA08i1OAnVKlPFjKBvk+r7jpsTPcr  
9pWXTO9JrYMML7d+XRSZA1n3856OqZDX4403+9FnXCvfcLZLLKTBvwwFgEFGpzjK  
UEVbkhd5qstF6qWK  
-----END CERTIFICATE-----  
subject=C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
  
issuer=C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
  
---  
No client certificate CA names sent  
Peer signing digest: SHA256  
Peer signature type: RSA  
Server Temp Key: DH, 2048 bits  
---  
SSL handshake has read 1988 bytes and written 629 bytes  
Verification error: self signed certificate  
---  
New, TLSv1.2, Cipher is DHE-RSA-AES256-GCM-SHA384  
Server public key is 2048 bit  
Secure Renegotiation IS supported  
Compression: NONE  
Expansion: NONE  
No ALPN negotiated  
SSL-Session:  
    Protocol  : TLSv1.2  
    Cipher    : DHE-RSA-AES256-GCM-SHA384  
    Session-ID: 6476915D6AE3C44B5CC2498B8066B67FFD2332A617500B0AA1AED04EB814E53C  
    Session-ID-ctx:  
    Master-Key: 798D5D5BFF616B9F5E74E32153F7938DDE9C4D4E2D2373F7B15D7E78B57C8D9FB7B89E69404D11AD226BA09DD4B26EB8  
    PSK identity: None  
    PSK identity hint: None  
    SRP username: None  
    TLS session ticket lifetime hint: 300 (seconds)  
    TLS session ticket:  
    0000 - a8 29 7c ae 20 98 25 d6-44 ff f1 b4 1d 6a 37 5b   .)|. .%.D....j7[  
    0010 - 26 ee 36 51 df ac 19 e7-ad 3f 3f 00 83 3b 5d 3e   &.6Q.....??..;]>  
    0020 - 16 8f 43 56 9b af 19 a1-46 c5 d0 37 eb 94 85 5b   ..CV....F..7...[  
    0030 - ff 83 8b 8f 3b 0a df 2a-10 9e 00 cf 94 2b 54 34   ....;..*.....+T4  
    0040 - 8a 3b ba c9 f6 f1 27 f2-14 55 ec 4f 2c f0 ce ab   .;....'..U.O,...  
    0050 - e5 2e 98 a0 21 08 22 b1-bf 0e a6 10 96 e6 1b 94   ....!.".........  
    0060 - 24 d0 0b 29 a5 29 a2 e3-8b 88 5a d8 72 0b 57 b0   $..).)....Z.r.W.  
    0070 - 97 cd 3b ae 18 a1 78 fe-71 37 1f ef ee 30 35 eb   ..;...x.q7...05.  
    0080 - 33 e6 77 e9 2b 75 d1 72-f6 d3 10 62 7f af f3 b1   3.w.+u.r...b....  
    0090 - 73 d1 a7 cf ee 13 47 b8-da 73 ee fb 4f 00 cc 1f   s.....G..s..O...  
    00a0 - da 50 13 42 0b 29 42 76-33 82 f0 0a 9b 73 e6 3a   .P.B.)Bv3....s.:  
  
    Start Time: 1550767931  
    Timeout   : 7200 (sec)  
    Verify return code: 18 (self signed certificate)  
    Extended master secret: no  
---  
GET /v1/status HTTP/1.1  
Authorization: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
AuthorizatioHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.n: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GET /HTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.v1/status HTTP/1.1  
Authorization: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
Authorization: HTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GET /v1/HTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.status HTTP/1.1  
Authorization: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
Authorization: BHTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.asic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GET /v1/HTTP/1.1 404 Not Found  
Server: Boost.Beast/216  
Content-Type: text/html  
Content-Length: 40  
  
The resource '/v1/status' was not found.status HTTP/1.1  
Authorization: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3  
Content-Length: 0  
  
GET /v1/status HTTP/1.1  
Authorization: depth=0 C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
verify error:num=18:self signed certificate  
verify return:1  
depth=0 C = US, ST = CA, L = Los AngelesO=BeastCN=www.example.com  
verify return:1  
DONE  
B  
vinnie@VF-Corsair MINGW64 /  
$  
```

---

## Comment 17

> Username: vinniefalco  
> Created at: 2019-02-21 16:56:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466077945  

You're right, your client (Is that what you're calling the bash/perl command?) only opens one connection, I added some logging and verified this. Also, I am using:  
```  
$ openssl version  
OpenSSL 1.1.1a  20 Nov 2018  
```

---

## Comment 18

> Username: Al2Klimov  
> Created at: 2019-02-21 16:58:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466078486  

Does increasing the 0.01 change anything?

---

## Comment 19

> Username: Al2Klimov  
> Created at: 2019-02-21 16:58:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466078513  

➜  poc-beast-bytewise git:(master) openssl version  
LibreSSL 2.6.5

---

## Comment 20

> Username: vinniefalco  
> Created at: 2019-02-21 17:02:19 UTC  
> Updated at: 2019-02-21 17:02:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466080207  

> Does increasing the 0.01 change anything?  
  
Yeah, it makes it a LOT slower lol.. I tried 0.1.  
  
I would suggest trying the **http-server-coro** example (without SSL) and modifying your client to use a plain connection. I believe that it will work correctly for you. And I think this will show that the problem has something to do with the SSL implementation.  
  
Note that Beast itself does not use OpenSSL, it is concept-driven. In fact Beast doesn't use sockets at all, it is up to the caller to provide a suitable socket or ssl stream object. Only the examples use actual sockets and ssl streams. So the library as written, should be correct. Problems like what you are experience, can only come from the ssl stream or the example program itself (which is very simple to audit).

---

## Comment 21

> Username: vinniefalco  
> Created at: 2019-02-21 17:11:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466083627  

> ➜ poc-beast-bytewise git:(master) openssl version  
> LibreSSL 2.6.5  
  
Another thing to try, is to use OpenSSL instead just for testing purposes. If it works, then we know that the problem is related to the SSL library.

---

## Comment 22

> Username: Al2Klimov  
> Created at: 2019-02-21 17:14:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466084917  

OK, I'll test that and report my results immediately after testing... did I understand you right that if there's a problem, it's originated by either the client or OpenSSL? Couldn't Beast/ASIO have a problem like if there's a break between the 100th byte and the next one... ?

---

## Comment 23

> Username: Al2Klimov  
> Created at: 2019-02-21 17:14:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466084945  

*OpenSSL 1.1.0g  2 Nov 2017* shows the same result. At the moment I'm compiling v1.1.1a.

---

## Comment 24

> Username: Al2Klimov  
> Created at: 2019-02-21 17:16:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466085722  

OK, just tested w/o SSL at all... works for me.

---

## Comment 25

> Username: vinniefalco  
> Created at: 2019-02-21 17:18:37 UTC  
> Updated at: 2019-02-21 17:18:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466086328  

> Couldn't Beast/ASIO have a problem like if there's a break between the 100th byte and the next one... ?  
  
Definitely not. Beast is designed from the ground up to handle the processing of data incrementally. The number of pieces that the HTTP message (or WebSocket message) is presented, or the size of the pieces, does not matter. The tests cover these cases extensively.

---

## Comment 26

> Username: Al2Klimov  
> Created at: 2019-02-21 17:19:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466086614  

LoL OpenSSL v1.1.1a fails completely:  
  
GET /v1/status HTTP/1.1  
Authorization: Basic cm9vdDowN2U4OWM2YmY3ZjJkKEYUPDATE  
4621542848:error:1420310A:SSL routines:SSL_key_update:wrong ssl version:ssl/ssl_lib.c:2090:  
O%                                                                                                                                ➜  poc-beast-bytewise git:(master) ✗

---

## Comment 27

> Username: vinniefalco  
> Created at: 2019-02-21 17:20:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466087026  

`parsegrind` is for HTTP messages like what `valgrind` is for an executable, this is used in the tests to ensure that the parser and stream algorithms are bullet-proof:  
https://github.com/boostorg/beast/blob/28f5921bd613d5076d640d094926a684731799c3/test/beast/http/basic_parser.cpp#L157

---

## Comment 28

> Username: vinniefalco  
> Created at: 2019-02-21 17:21:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466087328  

> 4621542848:error:1420310A:SSL routines:SSL_key_update:wrong ssl version:ssl/ssl_lib.c:2090:  
  
This error is happening before Beast sees any HTTP data, and is an SSL issue.

---

## Comment 29

> Username: Al2Klimov  
> Created at: 2019-02-21 17:31:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466090923  

OK, just tested against Google...  
  
```bash  
perl -e 'sleep 2; for (;;) { for my $c (split("", "GET /v1/status HTTP/1.1\r\nAuthorization: Basic cm9vdDowN2U4OWM2YmY3ZjJkOTQ3\r\nContent-Length: 0\r\n\r\n")) { print STDERR $c; print $c; $| = 1; select(undef, undef, undef, 0.01); } }; sleep 3600' |\  
openssl s_client -connect google.de:443  
```  
  
... with the same result. Either Google uses Beast+SSL, too or this is indeed an SSL problem on the client side.  
  
*Sigh*... if any of our customers will ever report issues happening with such edge-cased clients, I'll tell 'em what I think about that – the U.S. president way. 😉  
  
Anyway – thank you for everything. @Al2Klimov over 'n' out.

---

## Comment 30

> Username: vinniefalco  
> Created at: 2019-02-21 17:33:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466091552  

> Either Google uses Beast+SSL, too  
  
**LOL*** now that's funny...  
  
Just to make sure I understand this, it is a problem with `openssl s_client`?

---

## Comment 31

> Username: Al2Klimov  
> Created at: 2019-02-22 09:11:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1467#issuecomment-466327791  

Yes, I think so. If the communication to both the Beast usage example HTTPd and Google's one produce the same error, either it's a bug in `openssl s_client`... or it's a bug in *my* `openssl s_client` – which is more likely as *you* couldn't reproduce the bug.
