# #227 - intelligent compression [Closed]

> Username: vinniefalco  
> Created at: 2017-01-18 17:35:08 UTC  
> Updated at: 2024-06-04 12:51:30 UTC  
> Closed at: 2024-06-04 12:51:29 UTC  
> Url: https://github.com/boostorg/beast/issues/227  

When the message or frame is small or empty we might be smarter about the compression settings (or even if we compress at all).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-16 16:29:55 UTC  
> Url: https://github.com/boostorg/beast/issues/227#issuecomment-1157880400  

The config for the stream could have a size threshold parameter

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2022-10-10 07:11:15 UTC  
> Url: https://github.com/boostorg/beast/issues/227#issuecomment-1272871049  

For reference  
  
https://www.igvita.com/2013/11/27/configuring-and-optimizing-websocket-compression/

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2022-10-10 07:17:37 UTC  
> Url: https://github.com/boostorg/beast/issues/227#issuecomment-1272878006  

it seems this spec is already implemented: https://datatracker.ietf.org/doc/html/draft-ietf-hybi-permessage-compression  
  
is this issue still open or do we need to refine the logic?  
@vinniefalco

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-10-10 14:53:12 UTC  
> Url: https://github.com/boostorg/beast/issues/227#issuecomment-1273438522  

A simple algorithm is fine, a configurable size threshold below which, we don't compress.

---

## Comment 5

> Username: ashtum  
> Created at: 2024-06-04 12:51:29 UTC  
> Url: https://github.com/boostorg/beast/issues/227#issuecomment-2147459221  

Addressed in https://github.com/boostorg/beast/pull/2480/commits/10ad742752601ac18af0f712f255423bb6ee52ca.
