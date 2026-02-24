# #1683 - boost::beast with openssl [Closed]

> Username: klasing  
> Created at: 2019-08-14 18:30:17 UTC  
> Updated at: 2019-09-18 18:39:35 UTC  
> Closed at: 2019-09-18 18:39:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1683  

I created a self-signed certificate with openssl. I pasted the certificate data (a lot of characters) and the private key data (also a lot of characters) from this process into the appropriate files of my http_server_async_ssl example. _I didn't replace the DH parameters in the file: server_certificate.hpp_  
  
The server complains and refuses to connect with a client: **handshake: tlsv1 alert unknown ca**  
The client comes with the message: **handshake: certificate verify failed**  
A Edge browser, who's using the self-made certificate complains: **Foutcode: DLG_FLAGS_INVALID_CA** (Foutcode is Dutch, and means Errorcode) The browser does not connect to the server, according to the fact that the server does not create a session instance.  
  
When I ignore the certificate error and let the browser go unsecured to the server, the server responds to a GET request, according to the following output on the server side.  
`<<constructor>> listener()  
Remote endpoint 192.168.178.14:59075  
<<constructor>> session()  
Remote endpoint 192.168.178.14:59196  
<<constructor>> session()  
Remote endpoint 192.168.178.14:59204  
<<constructor>> session()  
Remote endpoint 192.168.178.14:59205  
<<constructor>> session()  
GET / HTTP/1.1  
Host: www.localhost.com  
Connection: Keep-Alive  
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8  
Accept-Language: nl-NL,nl;q=0.8,de-DE;q=0.7,de;q=0.5,en-US;q=0.3,en;q=0.2  
Upgrade-Insecure-Requests: 1  
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.102 Safari/537.36 Edge/18.18362  
Accept-Encoding: gzip, deflate, br`  
  
Can anybody help me to tackle this problem?

---

## Comment 1

> Username: djarek  
> Created at: 2019-08-19 17:36:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1683#issuecomment-522679359  

The browser doesn't trust your self signed certificate. You'll need to add the certificate to the browser's trust store, or add its CA (assuming you created your own CA) to the trust store.

---

## Comment 2

> Username: klasing  
> Created at: 2019-08-19 18:24:08 UTC  
> Updated at: 2019-08-19 18:25:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1683#issuecomment-522697356  

I placed my certificate in the Microsoft Management Console (MMC). Under: Console Root -> Certificates (Local Computer) -> Trusted Root Certification Authorities -> Certificates. I don't understand what you mean with 'the browser's trust store'. This note is also given in the boost::beast http_server_async_ssl example, but doesn't bring me very far. The problem is that, beside the browsers mistrust, the Operating System (Windows 10 OS) doesn't trust the certificate either.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-09-18 18:35:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1683#issuecomment-532812086  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: klasing  
> Created at: 2019-09-18 18:39:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1683#issuecomment-532813473  

Not solved. Not expecting a solution. Will therefore close.
