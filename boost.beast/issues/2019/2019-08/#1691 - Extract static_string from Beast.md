# #1691 - Extract static_string from Beast [Closed]

> Username: jm4R  
> Created at: 2019-08-30 07:49:42 UTC  
> Updated at: 2019-09-11 16:36:39 UTC  
> Closed at: 2019-09-11 16:36:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1691  

`static_string` is not strictly connected with network nor http. It could be very general-purpose class and might be very useful. Please consider extracting it from Beast:  
  
https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__static_string.html

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-02 00:43:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1691#issuecomment-526967734  

Extract it and put it where?

---

## Comment 2

> Username: jm4R  
> Created at: 2019-09-02 06:29:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1691#issuecomment-527019411  

> Extract it and put it where?  
  
Hello Vinnie,  
thanks for a great job with **Boost.Beast** at first!  
Under the hoods `static_string` is very similar with `static_vector` from **Boost.Container**. I think it suits better there. I understand that moving it to that module would somehow "transfer ownership" of this code and make a mess. So making very new module would also be a better idea than keep it inside Beast. There are already modules containing more-or-less one class, like **Boost.Optional**. What do you think?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-09-02 17:44:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1691#issuecomment-527213199  

> What do you think?  
  
What would be the benefit of extracting it, other than how it is spelled or included? You have to ask @igaztanaga, if he wants to add it to Boost.Container. Or do you mean that you want to write Boost.StaticString yourself and propose it?  
  
In principle I would prefer if someone else maintained `static_string`, as they can probably do a much better job than me especially by supporting C++03 or earlier, and having better compliance and testing, so if such a component were to appear elsewhere in Boost I would be happy to change Beast to simply reflect a type-alias to that component.

---

## Comment 4

> Username: jm4R  
> Created at: 2019-09-02 18:51:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1691#issuecomment-527223373  

> What would be the benefit of extracting it, other than how it is spelled or included?  
  
Boost is modularized for a good reason. Sometimes people don't want to have dependency of Beast, Asio, System, etc. just to be able to use static_string. Including `#include <boost/beast/core/static_string.hpp>` in project that does not even use beast is a bit ugly in my feeling.  
  
I see you don't want to maintanance such module, it would be additional work for you. I will ask Ion if he want to :)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-09-02 18:59:01 UTC  
> Updated at: 2019-09-02 18:59:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1691#issuecomment-527224752  

Beast is header-only so if you have Boost, then including static_string.hpp doesn't really include any of that other stuff, just little things like the header for Boost.Config (needed for any Boost library). And as far as I know, there is no "modular Boost" - you either have the whole thing, or you have nothing. For practical purposes, there is no meaningful disadvantage. But I understand your point.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-09-09 16:59:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1691#issuecomment-529572604  

I raised this question on the Boost mailing list

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-09-09 20:12:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1691#issuecomment-529645822  

I think going into its own library, e.g. Boost.FixedString, makes a lot of sense and would be beneficial to Beast and Boost.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-09-11 16:36:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1691#issuecomment-530462007  

See: https://github.com/vinniefalco/FixedString
