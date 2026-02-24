# #2528 - Long compilation time with microsoft compiler (v143) [Closed]

> Username: stavikpetr  
> Created at: 2022-10-05 14:10:11 UTC  
> Updated at: 2023-10-28 16:35:56 UTC  
> Closed at: 2023-10-28 16:35:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2528  

Hello, so just recently, we adopted beast for websocket client in our codebase. So far so good, it is working quite well, but I noticed that especially SSL async client just takes **_a lot_** of time to compile. In debug mode, one single file takes about 20 seconds to compile, which is about fifth of the whole time of one of our binaries.  
  
Just to confirm that this is not related to our codebase, I took the simple ssl async client from the examples in this repo. For debug, around 21 seconds, for release mode, around 30 seconds, which is quite crazy. (This was also the first time i had to use /bigobj option during compilation)  
  
Anything i can do with that? Or it's just the way it is ? :/  
  
Boost version: 1.78

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-10-05 14:41:17 UTC  
> Updated at: 2022-11-05 14:55:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2528#issuecomment-1268535569  

That's just the way it is, as of now, beast makes heavy use of templates. I'll leave the issue open,because I want to make some improvements here.

---

## Comment 2

> Username: stavikpetr  
> Created at: 2022-10-05 15:01:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2528#issuecomment-1268563932  

Ok thanks for the info.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2022-10-05 15:09:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2528#issuecomment-1268577118  

What completion method are you using, if I may ask? Lambdas as callbacks?

---

## Comment 4

> Username: stavikpetr  
> Created at: 2022-10-05 15:37:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2528#issuecomment-1268606142  

Sure, I am using the `beast::bind_front_handler` wrapper everywhere.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-10-06 04:50:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2528#issuecomment-1269308332  

You need to be careful to structure your source code so that you don't end up including all asio and beast even in the translation units that don't need it. A little bit of type erasing, and some adjustments to your business logic APIs will help.

---

## Comment 6

> Username: stavikpetr  
> Created at: 2022-10-06 06:51:55 UTC  
> Updated at: 2022-10-06 06:56:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2528#issuecomment-1269434101  

Yep, I understand. First I tried to reduce the number of includes and then I also tried precompiled headers. Still, around 20 seconds for the file with ssl async client, which was surprising (measured by vcperf).

---

## Comment 7

> Username: InternalHigh  
> Created at: 2022-11-05 11:44:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2528#issuecomment-1304504589  

Will that be fixed in Beast 2?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-11-05 21:34:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2528#issuecomment-1304646006  

It will be better yes but openssl and asio are big libraries, they need to be integrated into a project carefully. if you structure things right you can have your own library containing the network code which is built separately and might take a minute to build (but not require changes often) and then the calling code which will be fast to compile and link against the separate network lib.

---

## Comment 9

> Username: ashtum  
> Created at: 2023-08-18 07:46:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2528#issuecomment-1683506799  

@stavikpetr Could you please close the issue if your problem has been resolved?
