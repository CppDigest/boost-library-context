# #1947 - How to send request to expired https site? [Closed]

> Username: joeyskeys  
> Created at: 2020-05-08 11:09:40 UTC  
> Updated at: 2020-06-05 13:58:30 UTC  
> Closed at: 2020-06-05 13:58:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1947  

Hi all,  
  
I'm wondering if it is possible to send request to expired https site? Haven't actually tried yet but I think there should be some options like the browsers do, which will let you choose to visit anyway or close the site. Didn't find any relavent topic in the document.  
Gotta try it now and will update the result in this  issue. BTW, I'm using version 1.72.  
  
Any reply will be appreciated.

---

## Comment 1

> Username: akiirax  
> Created at: 2020-05-08 11:40:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1947#issuecomment-625775359  

If you mean skipping SSL certificate verification, you need to add this after creating your SSL context.  
  
`ctx.set_verify_mode(ssl::verify_none);`  
  
Explanation on what this SSL flag does, here https://www.openssl.org/docs/man1.0.2/man3/SSL_CTX_set_verify.html  
  
`set_verify_mode` documented here https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__ssl_stream.html

---

## Comment 2

> Username: joeyskeys  
> Created at: 2020-05-09 08:41:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1947#issuecomment-626130210  

@ahmedhat Yes, that's exactly what I need, thanks man.

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-06-05 13:58:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1947#issuecomment-639499634  

Closing this issue with reason: "Another satisfied customer".
