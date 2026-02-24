# #1 Remove BOOST_ATTRIBUTE_NORETURN [Merged]

> Username: Lastique  
> Created at: 2014-06-04 20:45:43 UTC  
> Updated at: 2014-06-04 21:31:04 UTC  
> Merged at: 2014-06-04 21:12:45 UTC  
> Closed at: 2014-06-04 21:12:45 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/1  

Remove BOOST_ATTRIBUTE_NORETURN and use BOOST_NORETURN provided by Boost.Config instead.

---

## Comment 1

> Username: Lastique  
> Created_at: 2014-06-04 20:46:45 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/1#issuecomment-45149142  

Moved from a previous pull request: https://github.com/boostorg/exception/pull/2.

---

## Comment 2

> Username: Lastique  
> Created_at: 2014-06-04 21:20:03 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/1#issuecomment-45153152  

A similar change is also needed in boost/exception/detail/exception_ptr.hpp. I've made that change as a part of a separate pull request: https://github.com/boostorg/exception/pull/1.

---

## Comment 3

> Username: Lastique  
> Created_at: 2014-06-04 21:27:22 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/1#issuecomment-45154033  

I should also add that without that change the compilation will likely fail now.

---

## Comment 4

> Username: pdimov  
> Created_at: 2014-06-04 21:28:17 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/1#issuecomment-45154146  

That pull request has too many changes in it, so I can't merge it. Can you please extract just the NORETURN changes?

---

## Comment 5

> Username: pdimov  
> Created_at: 2014-06-04 21:28:46 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/1#issuecomment-45154193  

It will fail as attribute_noreturn.hpp is no longer present.

---

## Comment 6

> Username: Lastique  
> Created_at: 2014-06-04 21:31:04 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/1#issuecomment-45154455  

Done. https://github.com/boostorg/exception/pull/3

---
