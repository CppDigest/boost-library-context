# #1534 - https "unknown ca" error in Beast v234; works in Boost::Beast-1.69.0 [Closed]

> Username: tlanc007  
> Created at: 2019-03-20 23:45:13 UTC  
> Updated at: 2019-03-22 02:54:09 UTC  
> Closed at: 2019-03-21 00:10:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1534  

### Version of Beast  
V 234 in develop  
  
### Steps necessary to reproduce the problem  
The following example runs fine under Boost::Beast-1.69.0.  However, under the latest development branch:  
the server gives:   
```handshake: tlsv1 alert unknown ca```  
the client gives:  
```handshake: certificate verify failed```  
  
The example is just using an example client and server from Beast (the async-ssl server and client).  
  
1) Start the server up: **http-server-async-ssl 127.0.0.1 8080 . 1**  
2) Run the client: **http-client-async-ssl 127.0.0.1 8080 / 1.1**  
  
I have tried beast v226, 233, and 234.  They all give the same broken response.  But the examples work fine under the official Beast from Boost-1.69.0.  
  
### All relevant compiler information  
MacOS 10.13.6, openSSL1.1.1a, clang-7.0.1  
  
I also tried the binaries on MacOS 10.14, openSSL1.0.2q and was seeing the same "unknown ca" error.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-20 23:47:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1534#issuecomment-475070874  

Unfortunately the certificates in the examples are "toy" certificates, and can't possibly be trusted to work reliably, as the examples do not contain the enormous amount of code required to access the operating-system certificate store (and check revocation list, and so on). If you want to do certificate verification you will need to use a library for it (I don't know of any that do it) or write your own code. cpprestsdk has some, and so does Chromium.  
  
The reason it worked in 1.69 is because the older examples allowed an insecure version of TLS/SSL, which has been fixed in 1.70.

---

## Comment 2

> Username: tlanc007  
> Created at: 2019-03-21 00:10:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1534#issuecomment-475074985  

Ok, noted.  
  
I did notice that there was a big change for v233.  But I thought that v226 was still using the older SSL bits.  But I guess that doesn't matter any more.  
  
So going forward for your client example, would you be switching out:  
```ctx.set_verify_mode(ssl::verify_peer);```  
  
with  
```set_verify_mode(ssl::verify_none);```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-03-21 00:11:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1534#issuecomment-475075231  

I'm not sure what to do to be honest. Certificate verification and TLS bits are out of scope for Beast, but I try to provide the examples as a reasonable starting point for users to explore their own solutions. As you probably already know, certificates are a giant headache. I'm open to ideas.

---

## Comment 4

> Username: djarek  
> Created at: 2019-03-21 22:33:45 UTC  
> Updated at: 2019-03-21 22:33:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1534#issuecomment-475428423  

@tlanc007 I've taken a look at this and it looks like it's working as intended in develop - the server certificate is not loaded into the client's trust store, so it's rejected in OpenSSL. If you want it to work with a self-signed certificate, either add the certificate to the OpenSSL store or load it in your program using `add_certificate_authority`.

---

## Comment 5

> Username: tlanc007  
> Created at: 2019-03-21 23:24:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1534#issuecomment-475439963  

@djarek  Isn't it being added via the `load_root_certificates(ctx);` statement made in the client example?    
  
**load_root_certificates.hpp**: `load_root_certificates ( ssl::context& ctx, boost::system::error_code& ec)`  calls `ctx.add_certificate_authority()` at the end of the function.  
  
I haven't tried adding it to the trust store directly in OpenSSL, though as I expected the `add_certificate_authority` to take care of that for me.

---

## Comment 6

> Username: djarek  
> Created at: 2019-03-21 23:28:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1534#issuecomment-475440947  

`load_root_certificates` loads a root certificate which is *not* the issuer of the certificate used by the server example (so the client doesn't trust the server, because it doesn't trust the issuer of its cert).

---

## Comment 7

> Username: tlanc007  
> Created at: 2019-03-21 23:30:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1534#issuecomment-475441425  

Ahh!  Got it.  Thanks.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-03-21 23:46:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1534#issuecomment-475444700  

`load_root_certificates` just loads two root CA certificates that I pulled off the Internet in order to get the examples to validate some domains, such as `www.example.com`. It is by no means a complete "certificate verification solution."  
  
We should probably add the server's certificate to `load_root_certificates` so that it is recognized though.

---

## Comment 9

> Username: tlanc007  
> Created at: 2019-03-22 00:07:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1534#issuecomment-475448563  

Yeah, I just dropped the server cert in with the root_certificates and it is now working.  
  
In this context, I wasn't looking for a final certificate solution.  I wanted to just be able to run some SSL tests against my server, without a need for a  full on certificate.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-03-22 00:42:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1534#issuecomment-475454333  

If you would like to submit a pull request, I can merge it

---

## Comment 11

> Username: tlanc007  
> Created at: 2019-03-22 02:54:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1534#issuecomment-475476381  

Ok.  
[PR#1537](https://github.com/boostorg/beast/pull/1537) submitted
