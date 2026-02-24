# #1710 - [Feature Request] stable interface for base64 encoding/decoding [Closed]

> Username: bgemmill  
> Created at: 2019-09-24 18:18:08 UTC  
> Updated at: 2022-07-29 00:05:32 UTC  
> Closed at: 2020-04-26 16:33:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1710  

This came up tangentially on [Stack Overflow](https://stackoverflow.com/questions/7053538/how-do-i-encode-a-string-to-base64-using-only-boost/48176443#48176443), would it be possible to promote the base64 encoding/decoding functions from the detail namespace?  
  
They're *really* useful, and far and away cleaner than the other pure-boost approaches.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-24 18:30:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1710#issuecomment-534689197  

If I do that, I will have to improve the algorithms to hold them to a higher standard, and they will need documentation and more robust tests. I will consider it!

---

## Comment 2

> Username: yost-jacob  
> Created at: 2019-11-01 09:49:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1710#issuecomment-548729217  

since you already rely on openssl wouldn't it make sense to just use the openssl tools?  Something like this on [Stack Overflow](https://stackoverflow.com/questions/5288076/base64-encoding-and-decoding-with-openssl/47981510#47981510)?  
  
certainly solves the algorithm problem.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-04-26 16:33:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1710#issuecomment-619581065  

You can just copy the code, it is small enough and I think copying it creates the least headache.

---

## Comment 4

> Username: sehe  
> Created at: 2022-07-28 22:23:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1710#issuecomment-1198687758  

@vinniefalco Can I refine this story to the point where we can make it happen? Perhaps it should become part of a more general purpose library. Could it be part of Boost Strings (algorithms) or even Core?  
  
Reason I think it's worth it is because of continuous support on SO. And in part the fact that the "other" Boost facility is ... terrible. Truly terrible (see https://stackoverflow.com/a/71933410/85371 (https://github.com/boostorg/serialization/issues/254), https://stackoverflow.com/a/72034755/85371, and more recently this thread https://stackoverflow.com/questions/70624519/boost-gzip-how-to-output-compressed-string-as-text/70625599#comment129071097_70625599)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-07-29 00:05:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1710#issuecomment-1198738659  

First check if Boost doesn't already have something. Second, yes I think there is room for prescription-strength radix conversions (think gigabytes per second streaming speed). However this doesn't belong in beast, and I don't have the bandwidth to think through a proper solution at the moment. Perhaps you should ask on the list?
