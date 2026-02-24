# #1129 - install open SSL [Closed]

> Username: snahmad  
> Created at: 2018-05-15 15:17:01 UTC  
> Updated at: 2018-07-18 20:40:32 UTC  
> Closed at: 2018-07-18 20:40:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1129  

### Version of Beast  
1.67  
  
### Steps necessary to reproduce the problem  
  
I am using boost beast with HTTPS( SSL) example which depends on open SSL.  
  
How to install or build open SSL on windows 7/10 machine.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-15 15:38:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-389212953  

These links look promising:  
  
https://www.cloudinsidr.com/content/how-to-install-the-most-recent-version-of-openssl-on-windows-10-in-64-bit/  
  
https://slproweb.com/products/Win32OpenSSL.html

---

## Comment 2

> Username: snahmad  
> Created at: 2018-05-15 16:36:16 UTC  
> Updated at: 2018-05-15 16:36:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-389232132  

I am building 32-bit executable on windows 7.   
So I need to use openSSL 32-bit. I am statically link   
C:\OpenSSL-Win32\lib\VC\static\libeay32MDd.lib  
  
C:\OpenSSL-Win32\lib\VC\static\ssleay32MDd.lib  
  
No luck.   
  
I am using project settings runtime : Multi-threaded Debug DLL (/MDd)  
  
Getting linker errors:  
  
libeay32MDd.lib(cryptlib.obj) : warning LNK4217: locally defined symbol _sscanf imported in function _OPENSSL_cpuid_setup  
1>libeay32MDd.lib(v3_utl.obj) : warning LNK4217: locally defined symbol _sscanf imported in function _X509V3_get_value_bool  
1>libeay32MDd.lib(cryptlib.obj) : warning LNK4217: locally defined symbol __vsnprintf imported in function _OPENSSL_showfatal  
1>libeay32MDd.lib(dso_win32.obj) : warning LNK4217: locally defined symbol _sprintf imported in function _win32_name_converter  
1>libeay32MDd.lib(pqueue.obj) : warning LNK4217: locally defined symbol _printf imported in function _pqueue_print  
1>libeay32MDd.lib(txt_db.obj) : error LNK2001: unresolved external symbol __imp____iob_func  
1>libeay32MDd.lib(cryptlib.obj) : error LNK2001: unresolved external symbol __imp____iob_func  
1>libeay32MDd.lib(pem_lib.obj) : error LNK2001: unresolved external symbol __imp____iob_func  
1>libeay32MDd.lib(ui_openssl.obj) : error LNK2001: unresolved external symbol __imp____iob_func  
1>ssleay32MDd.lib(t1_enc.obj) : error LNK2001: unresolved external symbol __imp____iob_func  
1>libeay32MDd.lib(pem_lib.obj) : error LNK2019: unresolved external symbol __imp__fprintf referenced in function _PEM_def_callback  
1>libeay32MDd.lib(ui_openssl.obj) : error LNK2001: unresolved external symbol __imp__fprintf  
1>ssleay32MDd.lib(t1_enc.obj) : error LNK2001: unresolved external symbol __imp__fprintf  
1>libeay32MDd.lib(txt_db.obj) : error LNK2001: unresolved external symbol __imp__fprintf  
1>C:\Work\LibDev\TestLibraries\Debug\TestLibraries.exe : fatal error LNK1120: 2 unresolved externals

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-05-15 18:13:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-389262589  

See:  
  
https://stackoverflow.com/questions/30450042/unresolved-external-symbol-imp-iob-func-referenced-in-function-openssldie/38836311

---

## Comment 4

> Username: snahmad  
> Created at: 2018-05-15 21:10:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-389314513  

Thanks. It is working without static linking. I will try static linking option as well.

---

## Comment 5

> Username: snahmad  
> Created at: 2018-05-15 21:10:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-389314685  

how  to import the "Beast Test CA" certificate intothe local certificate store, browser, or operating system

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-05-15 22:28:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-389333763  

The certificates that come with the Beast examples are not something you should be importing or using for production solutions. You will need to write your own certificate verification functions.

---

## Comment 7

> Username: snahmad  
> Created at: 2018-05-16 08:04:03 UTC  
> Updated at: 2018-05-16 08:04:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-389431615  

I need to use any free certificate for development to test not for production yet.  
I need to run the https://localhost:??/ in browser to test my server working with certificate.  
I will write sync http client using boost beast as well to test my server.

---

## Comment 8

> Username: snahmad  
> Created at: 2018-05-16 11:09:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-389481653  

This is self signed certificate I believe. Where can I find documentation for it.  
It is working with my boost beast ssl http client example but not from web browser.  
  
How can generate free certificate to test on my localhost.

---

## Comment 9

> Username: TechnikEmpire  
> Created at: 2018-06-09 07:00:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-395946261  

I doubt that cert generation is within the scope of beast. You can use openSSL's API's to generate a self signed CA (difficult) or you can Google your question and follow the millions of tutorials there are for generating a self signed CA using openSSL's command line tools.  
  
You can then Google how to encode your certificate for import into the windows certificate store by going from DER encoded to pkcs 12. Again, this can be done in code or by command line tools and Google will help you, and again, along with compiling openSSL, are probably beyond the scope of beast if I had to guess. There's a ton of tutorials and prebuilt versions available.  
  
Frankly, in my opinion, if you're asking these questions I would suggest you need to spend a fair bit of time reading everything you can find on all of these topics. However, it's pretty scarce and not well documented when you do find it. It's one thing to load Mozillas root ca list and do basic client verification with an SSL client stream (with dangers of it's own), it's another matter all-together to be a server and again to be a CA server.

---

## Comment 10

> Username: snahmad  
> Created at: 2018-06-11 07:40:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-396151107  

ok, sure. not working on HTTP with SSL in near feature. may look in few months time.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2018-06-11 13:22:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-396241405  

@TechnikEmpire A colleague of mine is going to start a project to build a library that focuses on certificate verification...how much do you know about it?

---

## Comment 12

> Username: TechnikEmpire  
> Created at: 2018-06-11 17:51:43 UTC  
> Updated at: 2018-06-11 18:32:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-396328870  

@vinniefalco Not enough to build something from scratch I can tell you that much. I let openSSL handle verification and, optionally, Microsoft's closed-source stack. You can look at Microsoft's [cpprestsdk](https://github.com/Microsoft/cpprestsdk) for reference if you're looking at leaning on existing libs like openssl. Specifically [here](https://github.com/Microsoft/cpprestsdk/blob/19b67d592bad60a44ed8a20ba7505fb6f468b566/Release/src/http/client/x509_cert_utilities.cpp).  
  
However, I'm pretty sure even Microsoft's attempt there with openSSL isn't quite right (last time I looked at the code). Pretty sure you need to manually inspect SAN's (in the case of TLS/HTTPS) as part of the verification process to ensure that a valid certificate applies to the connected domain. `asio::ssl` has a class in it called `rfc_rfc2818_validation` that is supposed to do this for you. My memory is a little hazy on that subject but I remember this being insufficient and I possibly had to manually decompose the SAN list and do comparison or something of this nature.  
  
Plus (also missing from there) I've seen situations where people like Google will get an intermediary CA issued by something like DigitCert high-trust CA, then use it to mint additional intermediary CA's or issue host certs directly. The problem is that, though the chain should be complete, it's sometimes not and even in this nightmare scenario, so you wind up failing validation (Microsoft's code fails the same way). I'm not sure how Google and folks in browsers solve this, but they might be leaning on the OCSP query system to discover missing CA's in the chain and dynamically adding newly discovered and verified intermediate CA's via the AIA (Authority Information) extension.  
  
You also need to lean on a static CRL, or you lean on OCSP queries and responses for dynamic CRL checks.  
  
There's the sum of my knowledge on the subject. lol again, if this person is building a from-scratch crypto library that does independent verification, I'm super-unqualified for that.  
  
Best.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2018-06-11 18:12:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-396335367  

Wow sounds like you know quite a lot!!! I don't know much at all.  
  
> if this person is building a from-scratch crypto library  
  
No, not from-scratch crypto. It will use `boost::asio::ssl::context` and OpenSSL calls, combined with platform-specific APIs (to retrieve the installed root certificates). But it will need to address all the points you raised (they are all in-scope).  
  
@djarek ping ^^

---

## Comment 14

> Username: TechnikEmpire  
> Created at: 2018-06-11 18:33:26 UTC  
> Updated at: 2018-06-11 18:39:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-396341893  

@vinniefalco @djarek So I'm not the greatest C++ developer and this project got messy because it ballooned fast but, nearly everything I know on this topic can be found in [these](https://github.com/TechnikEmpire/HttpFilteringEngine/tree/master/src/te/httpengine/mitm/secure) files.  
  
I ended up abandoning that proxy and moving to C# because the maintenance cost got too high and so I let Microsoft have headaches over issues like proper chain discovery and verification. :) In the latest version of that lib, I delegated to Microsoft's restsdk code but again it is insufficient. I dropped it before diving into openSSL's OCSP query system or bothering to figure out a good way to parse any URL's in the AIA extension (if present), fetch the pointed cert and then include it in the verification chain.   
  
That's going to be a headache, but if you can add that on top of the `rfc_rfc2818_validation`, then you should be golden and all you'll have to do is use the provided TLS extensions for OCSP CRL information and/or load a static CRL file at startup.  
  
That code base is overloaded with comments explaining everything ad nauseam but if I can help any further just ping me or email me or whatever.  
  
Edit  
It's super easy to build tests around this https://badssl.com/ to make sure you're configuring OpenSSL properly.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2018-06-11 19:09:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-396353088  

That is all **extremely helpful** !!!!  
  
Also, check this out:  
https://github.com/chromium/chromium  
  
^^ Somewhere in there is code for doing certificate verification on various platforms (can't seem to find it at the moment)

---

## Comment 16

> Username: TechnikEmpire  
> Created at: 2018-06-11 19:12:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-396353986  

@vinniefalco Yeah I'm just looking at forking Chromium to start a mobile browser with built-in content filtering so once I start learning my way around that code base, I'll report back anything helpful I find out there.

---

## Comment 17

> Username: stale[bot]  
> Created at: 2018-07-11 20:09:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-404294423  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 18

> Username: stale[bot]  
> Created at: 2018-07-18 20:40:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1129#issuecomment-406067008  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
