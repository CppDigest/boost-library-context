# #117 - Add pilfer to Core [Open]

> Username: vinniefalco  
> Created at: 2022-05-15 23:57:02 UTC  
> Updated at: 2022-05-16 15:07:34 UTC  
> Url: https://github.com/boostorg/core/issues/117  

Add pilfer to Core so that it can be used by more than just Boost.JSON

---

## Comment 1

> Username: Lastique  
> Created at: 2022-05-16 11:18:01 UTC  
> Url: https://github.com/boostorg/core/issues/117#issuecomment-1127543562  

I don't understand what's this about. Closing as spam.  
  
@vinniefalco Please be clear about the problem description in future issues and avoid jargonisms.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-05-16 11:59:01 UTC  
> Url: https://github.com/boostorg/core/issues/117#issuecomment-1127579599  

It's a request to add `pilfer` to Core. `pilfer` currently exists in Boost.JSON detail, and has been proposed by me in [P0308](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0308r0.html).  
  
That'd actually be a legitimate addition to Core, for a change - the component is already present in another library and is needed as common infrastructure in others.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-05-16 12:19:50 UTC  
> Url: https://github.com/boostorg/core/issues/117#issuecomment-1127602220  

See: https://github.com/boostorg/json/blob/98d37f93706b50fd057d14bd241a2bfbcfec1527/include/boost/json/pilfer.hpp#L27

---

## Comment 4

> Username: breese  
> Created at: 2022-05-16 13:52:22 UTC  
> Url: https://github.com/boostorg/core/issues/117#issuecomment-1127704082  

You may want to reconsider the name. This facility is a optimization on move semantics, and is thus special-purpose and less widely used. In such cases we should consider a longer, more expressive name, such as `semi_destructive_move()`, to make it more likely that arbitrary users understand the code without having to read up on "pilfering".

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-05-16 15:07:34 UTC  
> Url: https://github.com/boostorg/core/issues/117#issuecomment-1127793234  

I like `pilfer`
