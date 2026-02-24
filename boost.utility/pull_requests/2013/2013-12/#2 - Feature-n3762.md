# #2 Feature/n3762 [Closed]

> Username: pabigot  
> Created at: 2013-12-24 20:20:36 UTC  
> Updated at: 2018-05-15 01:49:55 UTC  
> Closed at: 2018-05-15 01:49:55 UTC  
> Url: https://github.com/boostorg/utility/pull/2  

This set of commits fixes several bugs in Boost.String_ref, and brings the interface closer to the current draft proposal.  The tests pass on gcc (4.6.3 + 4.9.0-dev, with and without -std=c++11) and with clang (3.0 and 3.4-dev).  The remaining interface inconsistencies are listed in the user documentation.

---

## Comment 1

> Username: pabigot  
> Created_at: 2014-01-19 16:57:49 UTC  
> Url: https://github.com/boostorg/utility/pull/2#issuecomment-32712940  

Added a commit to fix the default constructed value producing a null data().

---

## Comment 2

> Username: danieljames  
> Created_at: 2014-05-28 15:08:20 UTC  
> Url: https://github.com/boostorg/utility/pull/2#issuecomment-44419146  

I assigned this to @mclow as I think he's the maintainer of this header.

---

## Comment 3

> Username: mclow  
> Created_at: 2014-05-28 16:09:20 UTC  
> Url: https://github.com/boostorg/utility/pull/2#issuecomment-44428880  

I'm planning a major update to string_ref, because there were a bunch of changes made in Issaquah. I will incorporate much of this pull request in there.

---
