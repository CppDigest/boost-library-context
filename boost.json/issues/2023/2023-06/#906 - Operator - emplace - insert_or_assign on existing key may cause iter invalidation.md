# #906 - Operator[] / emplace / insert_or_assign on existing key may cause iter invalidation [Closed]

> Username: mokeevdmitrii  
> Created at: 2023-06-09 10:40:30 UTC  
> Updated at: 2023-06-10 21:09:59 UTC  
> Closed at: 2023-06-10 21:09:59 UTC  
> Url: https://github.com/boostorg/json/issues/906  

### Version of Boost  
  
1.82  
  
### Steps necessary to reproduce the problem  
  
https://godbolt.org/z/MeKzYbqsv  
  
Crashes, because `operator []` called with existing key as an argument causes reallocation and iterator invalidation ignoring the fact the key hasn't really been inserted. According to docs, iterators are not affected if the key isn't inserted.  
  
### All relevant compiler information  
  
LLVM Clang 16.0.0  
Originally found on MacOS Ventura 13.2.1  
  
#### Notes   
Relevant documentation for operator[] ([reference](https://www.boost.org/doc/libs/master/libs/json/doc/html/json/ref/boost__json__object/operator_lb__rb_.html)) declares that iterator invalidation happens only on successful value insertion. However, [as it can be found in source code](https://github.com/boostorg/json/blob/ee01580cfde818e31d8ea41c2e4056bcff9e3018/include/boost/json/impl/object.ipp#L598) for boost 1.82 (older versions too, probably), operator[] works via calling `emplace(key, nullptr)`.   
  
The real "problem" lies in the `boost::json::object::emplace` function. It is said in the docs it works the same way as `[]` - invalidation only upon insertion. Unfortunately, it's not stated that `emplace` always ([ref to boost 1.82 source](https://github.com/boostorg/json/blob/ee01580cfde818e31d8ea41c2e4056bcff9e3018/include/boost/json/impl/object.hpp#L410)) reserves memory for at least `size() + 1` elements before checking should the element be inserted or not - so iterators **could** be invalidated (if you are unlucky) even on unsuccessful insertion.  
  
#### Suggested change  
  
Haven't prepared the MR though, but I suppose it would be enough to move lines with memory reservation in `boost::json::object::emplace` and `boost::json::object::insert_or_assign` below checking for existing with (maybe) some extra checks (as `detail::find_in_object` asserts for non-zero capacity, for example).

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-06-09 10:45:14 UTC  
> Url: https://github.com/boostorg/json/issues/906#issuecomment-1584373770  

This is technically a documentation issue, but I'm sure we should be able to make these functions provide the basic guarantee.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2023-06-09 12:09:53 UTC  
> Url: https://github.com/boostorg/json/issues/906#issuecomment-1584474788  

I tried my best to offer the strong guarantee everywhere, it would be disappointing to not achieve that

---

## Comment 3

> Username: mokeevdmitrii  
> Created at: 2023-06-09 14:48:09 UTC  
> Url: https://github.com/boostorg/json/issues/906#issuecomment-1584706060  

actually found this to be a duplicate of https://github.com/boostorg/json/issues/763, so you may close this one :)
