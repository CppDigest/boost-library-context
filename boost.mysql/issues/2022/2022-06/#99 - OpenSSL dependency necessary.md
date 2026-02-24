# #99 - OpenSSL dependency necessary? [Closed]

> Username: 3n16m4  
> Created at: 2022-06-06 15:23:39 UTC  
> Updated at: 2023-08-20 16:17:19 UTC  
> Closed at: 2022-06-06 17:11:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/99  

First of all: Great library! Like really, really great. The interface is almost as if the library has already been in boost for several years and everything makes sense without digging too much into the documentation. I hope it makes it through the proposal process :)  
  
I have one concern on the requirements for the use of this library though: Is the dependency OpenSSL really necessary? In my case, I'm running a local docker container with a MySQL server that I use without SSL but I still require to link it with OpenSSL and provide its headers. Forgive my ignorance but couldn't this be solved with macros to avoid compiling SSL based code when no OpenSSL was found? Right now it's not a problem, just inconvenient as I'm never planning to use OpenSSL 😅  
  
Thanks!

---

## Comment 1

> Username: anarthal  
> Created at: 2022-06-06 17:11:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/99#issuecomment-1147681053  

Hi, thanks.  
  
OpenSSL is used for hashing passwords during the authentication phase (invoked by `connection::connect`), even if you are not using SSL/TLS enabled connections.  
  
Regards,  
Ruben.

---

## Comment 2

> Username: serfcity  
> Created at: 2023-08-17 17:11:10 UTC  
> Url: https://github.com/boostorg/mysql/issues/99#issuecomment-1682664741  

This functionality can be in #ifdef SSL_REQUIREMENT ... #else Boost.Mysql realization of this crypto function #endif

---

## Comment 3

> Username: anarthal  
> Created at: 2023-08-17 18:08:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/99#issuecomment-1682740273  

The MySQL protocol mandates that all passwords must be sent hashed, even if you're not using encrypted connections. OpenSSL is used by `connection::handshake` and `connection::connect` to perform such hashing, even if using non-encrypted connections.  
  
Even if SSL-related functionality was if-def'ed out, OpenSSL would still be required.

---

## Comment 4

> Username: serfcity  
> Created at: 2023-08-17 19:52:22 UTC  
> Url: https://github.com/boostorg/mysql/issues/99#issuecomment-1682883862  

How does MySQL++ and MySQL C API work then?  
They do not require OpenSSL.

---

## Comment 5

> Username: anarthal  
> Created at: 2023-08-17 22:07:44 UTC  
> Updated at: 2023-08-17 22:09:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/99#issuecomment-1683040627  

The MySQL C API uses OpenSSL internally:  
  
```  
$ ldd /usr/lib/x86_64-linux-gnu/libmysqlclient.so.21.2.34  
        linux-vdso.so.1 (0x00007fff5cba6000)  
        libssl.so.3 => /lib/x86_64-linux-gnu/libssl.so.3 (0x00007fa51095c000)  
        libcrypto.so.3 => /lib/x86_64-linux-gnu/libcrypto.so.3 (0x00007fa510400000)  
        libresolv.so.2 => /lib/x86_64-linux-gnu/libresolv.so.2 (0x00007fa511111000)  
        libzstd.so.1 => /lib/x86_64-linux-gnu/libzstd.so.1 (0x00007fa51088d000)  
        libstdc++.so.6 => /lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007fa510000000)  
        libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007fa5110ef000)  
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fa50fc00000)  
        /lib64/ld-linux-x86-64.so.2 (0x00007fa511143000)  
        libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007fa510319000)  
```  
  
As libmysqlclient is a compiled library, you only need the binary, not the headers. Boost.MySQL is header-only, and thus requires the headers, too. mysqlpp is a wrapper around libmysqlclient, so the same applies.  
  
The only option I'd see would be reimplementing `SHA256`, which I'm not keen on. What's the rationale behind trying to avoid the openssl dependency?

---

## Comment 6

> Username: serfcity  
> Created at: 2023-08-18 06:54:36 UTC  
> Url: https://github.com/boostorg/mysql/issues/99#issuecomment-1683440388  

Ok, and how to recompile boost.mysql with LibreSSL or GNUTLS?

---

## Comment 7

> Username: serfcity  
> Created at: 2023-08-18 06:56:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/99#issuecomment-1683442338  

OpenSSL have a lot of security bugs: https://www.openssl.org/news/vulnerabilities.html  
SHA256 really easy algorithm: http://www.zedwood.com/article/cpp-sha256-function

---

## Comment 8

> Username: anarthal  
> Created at: 2023-08-18 09:28:51 UTC  
> Url: https://github.com/boostorg/mysql/issues/99#issuecomment-1683631355  

If you've found a day-zero vuln on OpenSSL let me know ;)  
  
From what I know, LibreSSL is API compatible with OpenSSL, so change your `find_package(OpenSSL REQUIRED)` for whatever LibreSSL offers and link against it.

---

## Comment 9

> Username: serfcity  
> Created at: 2023-08-18 10:22:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/99#issuecomment-1683698674  

First of all СVEs were discovered before they were fixed by OpenSSL developers.  
Also why I should link (and distribute and so on) OpenSSL library, if I don't use it?  
Is 30 string of SHA256 implementation impossible requirement for boost.mysql?

---

## Comment 10

> Username: anarthal  
> Created at: 2023-08-18 10:43:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/99#issuecomment-1683722008  

It's not a 30 string implementation, it's a bunch of testing, several CI builds to verify eveything works without OpenSSL, and a big refactor on how to detect / type erase SSL streams without pulling any `asio/ssl`  OpenSSL headers. It's not impossible, but it's not small at all.

---

## Comment 11

> Username: serfcity  
> Created at: 2023-08-19 06:49:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/99#issuecomment-1684865048  

If I remember correctly, only 1 function was not link without -lssl. This is the reason to keep using MySQL++ instead of Boost.MySQL. And how Boost.MySQL work with Boost.asio with other crypto library: https://github.com/boostorg/asio/blob/28ff1e7c2b44f141eb809abaf2d76c95f38350ca/include/boost/asio/ssl/detail/openssl_types.hpp#L20 ?

---

## Comment 12

> Username: anarthal  
> Created at: 2023-08-19 11:03:48 UTC  
> Url: https://github.com/boostorg/mysql/issues/99#issuecomment-1684919332  

Are you currently using MySQL++ successfully? What's your motivation to move to Boost.MySQL?

---

## Comment 13

> Username: serfcity  
> Created at: 2023-08-20 16:17:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/99#issuecomment-1685322550  

> Are you currently using MySQL++ successfully?  
Yes, of course.  
> What's your motivation to move to Boost.MySQL?  
Because I use other Boost libraries in my project. That's united "eco-system" of Boost :-)
