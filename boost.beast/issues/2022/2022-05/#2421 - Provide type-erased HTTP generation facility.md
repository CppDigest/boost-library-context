# #2421 - Provide type-erased HTTP generation facility [Closed]

> Username: sehe  
> Created at: 2022-05-03 17:18:05 UTC  
> Updated at: 2022-06-14 22:03:43 UTC  
> Closed at: 2022-06-14 22:03:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2421  

Referring to earlier discussion such as #1139 there is a need for interfaces that do not depend on the static type of the http message object (which is templated).  
  
@vinniefalco made a prototype here:   
  
> Have a look at these:  
>   
> https://github.com/vinniefalco/BeastLounge/blob/44194d746fdb37376ddd998b888389eff7c27ef7/src/http_generator.hpp  
> https://github.com/vinniefalco/BeastLounge/blob/44194d746fdb37376ddd998b888389eff7c27ef7/src/buffers_generator.hpp#L128  
>   
> Usage:  
>   
> https://github.com/vinniefalco/BeastLounge/blob/44194d746fdb37376ddd998b888389eff7c27ef7/src/http_service.cpp#L157  
> https://github.com/vinniefalco/BeastLounge/blob/44194d746fdb37376ddd998b888389eff7c27ef7/src/http_connection.cpp#L147  
>

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-05-03 18:25:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2421#issuecomment-1116419798  

I love it!  
  
Conflict of interest disclosure: I am the author.

---

## Comment 2

> Username: sehe  
> Created at: 2022-05-10 04:57:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2421#issuecomment-1121932281  

Reflecting discussion on cppslack:  
  
 - the generator is intended as an ephemeral type (storage cannot be re-used because of one-time BodyWriter types, e.g.)  
 - the samples should be refactored to use the new facility  
  
@vinniefalco New questions:  
  
`http_generator` already exposes `keep_alive` to interrogate the type-erased message. Should serializer features also be exposed (as long as they don't depend on message template parameters). Specifically `limit(size_t)` could be required/useful? Perhaps even `split`/`is_header_done`?

---

## Comment 3

> Username: sehe  
> Created at: 2022-05-10 05:39:13 UTC  
> Updated at: 2022-05-10 05:45:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2421#issuecomment-1121953241  

Having looked more closely at `limit` I'm going to vote against (it might be more relevant already to tune the `max_buf` parameter).  
  
As for `split`/`is_header_done` I'm also gravitating towards "don't reimplement serializer interface", but maybe I don't understand the `Expect: 100-continue` use-case fully, so I'd like to check. At least none of the examples require it.  
  
Update: split support would require additional logic and state in the generator to prevent calling `next()` before the consumer has been able to detect `is_header_done()`. That's a no verdict.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-05-10 16:30:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2421#issuecomment-1122620631  

This is starting to sound like more trouble than it is worth. The current examples don't handle 100-continue (or do they?)

---

## Comment 5

> Username: sehe  
> Created at: 2022-05-10 16:31:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2421#issuecomment-1122622187  

Indeed ("At least none of the examples require it."). So I'd skip that until maybe someone in the future sells the use-case

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-06-14 17:34:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2421#issuecomment-1155498894  

Is this resolved?

---

## Comment 7

> Username: sehe  
> Created at: 2022-06-14 22:03:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2421#issuecomment-1155755024  

I think so, except maybe for dreams of better docs surrounding it. I guess that having all the examples use it is the most important kind of documentation, but as an end user I'd love to see the same in the overview documentation.
