# #498 - Fix ureal_policies use of std::numeric_limits<T>::max_digits10 [Closed]

> Username: djowel  
> Created at: 2019-04-25 12:04:40 UTC  
> Updated at: 2019-04-26 03:01:04 UTC  
> Closed at: 2019-04-26 03:00:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/498  

Fix issue noted in https://github.com/boostorg/spirit/commit/949fe02a1f1cc8eb13fadfafb0fcecd740793d91  
  
Here: https://github.com/boostorg/spirit/blob/949fe02a1f1cc8eb13fadfafb0fcecd740793d91/include/boost/spirit/home/qi/numeric/real_policies.hpp#L40

---

## Comment 1

> Username: djowel  
> Created at: 2019-04-26 03:01:04 UTC  
> Url: https://github.com/boostorg/spirit/issues/498#issuecomment-486908435  

Fixed in https://github.com/boostorg/spirit/commit/8d5429ad7b69a6a1b479f4216ef200a368472de2
