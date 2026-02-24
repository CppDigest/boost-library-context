# #104 Add support for the binary parsers [Merged]

> Username: teajay-fr  
> Created at: 2015-05-23 13:34:00 UTC  
> Updated at: 2015-05-27 06:08:49 UTC  
> Merged at: 2015-05-27 06:08:49 UTC  
> Closed at: 2015-05-27 06:08:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/104  

This is not polished yet, and doesn't support floating point types. Consider this as pre-review pull request.  
  
Comments are expected and welcome.

---

## Comment 1

> Username: vtnerd  
> Created_at: 2015-05-23 14:05:21 UTC  
> Updated_at: 2015-05-24 19:51:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/104#discussion_r30945154  

Should the template be removed from this function? If it were limited to type `T`, a user would get an error immediately if it couldn't be converted. Currently a conversion error won't occur until the parse function of `binary_lit_parser`.

---

## Comment 2

> Username: vtnerd  
> Created_at: 2015-05-23 14:12:36 UTC  
> Updated_at: 2015-05-24 19:51:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/104#discussion_r30945211  

Should this be done in the constructor? The user would pay a small penalty if the parse function was never called, but if it were called more than once, it would be a small benefit.  
  
Similarly, the move_to function below does any endianness conversion lazily. Its possible to do the conversion immediately in the constructor too. However, a native type could be storing a value in the wrong endianness (which goes against the purpose of the `boost::endian` library).  
  
If the v2 version does it this way, perhaps it should duplicated.

---

## Comment 3

> Username: djowel  
> Created_at: 2015-05-23 14:59:21 UTC  
> Updated_at: 2015-05-24 19:51:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/104#discussion_r30945501  

Maybe use enum class here instead?

---

## Comment 4

> Username: djowel  
> Created_at: 2015-05-23 15:03:35 UTC  
> Updated_at: 2015-05-24 19:51:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/104#discussion_r30945534  

Aside (not so important): This pragma once for msvc has ben bugging me forever :) 1) is it really that significant? 2) are there no pragma once in other compilers. this is quite prevalent in spirit.

---

## Comment 5

> Username: djowel  
> Created_at: 2015-05-23 15:05:03 UTC  
> Updated_at: 2015-05-24 19:51:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/104#discussion_r30945545  

remove msvc gunk for now?

---

## Comment 6

> Username: djowel  
> Created_at: 2015-05-23 15:06:02 UTC  
> Updated_at: 2015-05-24 19:51:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/104#discussion_r30945556  

use auto ;-)

---

## Comment 7

> Username: djowel  
> Created_at: 2015-05-23 15:08:03 UTC  
> Updated_at: 2015-05-24 19:51:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/104#discussion_r30945574  

:-)

---

## Comment 8

> Username: djowel  
> Created_at: 2015-05-23 15:51:20 UTC  
> Updated_at: 2015-05-24 19:51:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/104#discussion_r30945857  

This might undergo some changes still once I get 'lazy' parsers back in. But you can ignore my comment form now if that does not seem make any sense.

---

## Comment 9

> Username: K-ballo  
> Created_at: 2015-05-23 18:10:01 UTC  
> Updated_at: 2015-05-24 19:51:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/104#discussion_r30946833  

1) Not that significant, as compilers have learned to recognize header guards to introduce the same behavior `#pragma once` would.  
2) It's supported by several compilers according to the definition of `BOOST_HAS_PRAGMA_ONCE`.

---

## Comment 10

> Username: teajay-fr  
> Created_at: 2015-05-23 20:03:01 UTC  
> Updated_at: 2015-05-24 19:51:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/104#discussion_r30947814  

According to Wikipedia most of the c++ compiler support it, except solaris studio. I'll check how BOOST_HAS_PRAGMA_ONCE is supposed to be used.

---

## Comment 11

> Username: teajay-fr  
> Created_at: 2015-05-23 20:03:34 UTC  
> Updated_at: 2015-05-24 19:51:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/104#discussion_r30947819  

Yes. Doesn't make sense right now.

---

## Comment 12

> Username: djowel  
> Created_at: 2015-05-24 06:38:47 UTC  
> Updated_at: 2015-05-24 19:51:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/104#discussion_r30950600  

I think we should just remove all pragma once. Header guards should suffice. ( i just removed all pragma once in x3 )

---

## Comment 13

> Username: teajay-fr  
> Created_at: 2015-05-25 06:44:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/104#issuecomment-105141081  

Just pushed a finalized version of the binary parser. Floating point support will have to wait until the endian library support it. It is planned for 1.59.

---

## Comment 14

> Username: djowel  
> Created_at: 2015-05-27 06:08:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/104#issuecomment-105768658  

I'll merge this. let us refine later.

---
