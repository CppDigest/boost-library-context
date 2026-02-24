# #1692 - Using client authentication certificate [Closed]

> Username: amir-ahmed-ansari  
> Created at: 2019-09-02 14:23:37 UTC  
> Updated at: 2019-10-11 18:29:22 UTC  
> Closed at: 2019-10-11 18:29:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1692  

In Boost 1.70, I cannot find any way to specify a client authentication certificate, so the server can authenticate the client in addition to the client verifying the server's certificate. This is possibly a documentation issue since I am sure OpenSSL supports this. Unfortunately, I do not have enough experience with OpenSSL to determine the best course of action.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-02 20:03:31 UTC  
> Updated at: 2019-09-02 20:20:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1692#issuecomment-527234801  

> I do not have enough experience with OpenSSL to determine the best course of action.  
  
Me neither.

---

## Comment 2

> Username: djarek  
> Created at: 2019-09-04 16:30:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1692#issuecomment-527980909  

@amir-ahmed-ansari you can either use this function: https://www.openssl.org/docs/man1.1.1/man3/SSL_CTX_set_client_cert_cb.html and an appropriate callback to set the certificate when the server asks for one (e.g. you can show a prompt for a certificate to the user). Alternatively, you can also just set the certificate upfront using the `use_certificate` family of functions: https://www.boost.org/doc/libs/1_71_0/doc/html/boost_asio/reference/ssl__context/use_certificate.html or https://www.boost.org/doc/libs/1_71_0/doc/html/boost_asio/reference/ssl__context/use_certificate_file.html

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-10-04 17:29:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1692#issuecomment-538489811  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-10-11 18:29:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1692#issuecomment-541174654  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
