# #2046 - is HMAC SHA256 provided by beast? [Closed]

> Username: niXman  
> Created at: 2020-08-08 16:00:25 UTC  
> Updated at: 2020-08-09 11:31:42 UTC  
> Closed at: 2020-08-09 11:31:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2046  

Hi,  
  
can you tell me please is beast provides an HMAC SHA256 implementation without using third party libraries like openssl?  
if yes - can you please point me?  
  
thanks!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-08 16:05:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2046#issuecomment-670945291  

Beast does not provide SHA256. But you don't need to use OpenSSL, just copy one of the many 3rd party C++ SHA256 libraries. For example, this one comes in a single header file:  
https://github.com/okdshin/PicoSHA2

---

## Comment 2

> Username: niXman  
> Created at: 2020-08-08 16:11:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2046#issuecomment-670945986  

got it, thank you!  
  
ps  
I just use `beast` in my project and thought that maybe for `HMAC SHA256` I can use something from `beast` instead of `openssl`.

---

## Comment 3

> Username: niXman  
> Created at: 2020-08-09 09:04:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2046#issuecomment-671027010  

i was able to do it using `picohash` project but when I removed `libcrypto` and `libssl` from linker cmdline i faced with unresolved externals to `openssl` because `asio` uses `openssl` internaly =)  
will revert.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-08-09 11:10:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2046#issuecomment-671038483  

Well, if you are already using OpenSSL (for `asio::ssl::stream`) then there's nothing wrong with using the SHA256 from OpenSSL.

---

## Comment 5

> Username: niXman  
> Created at: 2020-08-09 11:31:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2046#issuecomment-671040587  

right, but I forgot `asio` also uses `openssl`.
