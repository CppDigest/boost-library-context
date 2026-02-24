# #14 Fix metaprogramming tests about references [Closed]

> Username: alkino  
> Created at: 2015-04-30 23:59:50 UTC  
> Updated at: 2015-09-12 15:16:20 UTC  
> Closed at: 2015-09-12 15:16:20 UTC  
> Url: https://github.com/boostorg/variant/pull/14  

boost::get<const int&> doesn't work either.  
  
Because boost::remove_cv<const int&> return "const int &". Because it try to remove const to top type which is the reference.

---

## Comment 1

> Username: de-vri-es  
> Created_at: 2015-05-04 14:01:19 UTC  
> Url: https://github.com/boostorg/variant/pull/14#issuecomment-98715322  

You could include only the needed type_trait headers (remove_cv.hpp and remove_reference.hpp), although I'm not sure if there is a preference for doing that rather than including them all.

---

## Comment 2

> Username: alkino  
> Created_at: 2015-05-04 14:05:26 UTC  
> Url: https://github.com/boostorg/variant/pull/14#issuecomment-98718299  

I don't know in fact :s

---

## Comment 3

> Username: apolukhin  
> Created_at: 2015-05-05 19:52:15 UTC  
> Url: https://github.com/boostorg/variant/pull/14#issuecomment-99202070  

Thanks for the patch, but I did not get it. What does not work?  
  
Please provide an example and make a test from it. And next time make a pull request against `develop` branch, not `master`.

---

## Comment 4

> Username: apolukhin  
> Created_at: 2015-05-05 19:56:32 UTC  
> Url: https://github.com/boostorg/variant/pull/14#issuecomment-99202857  

Oops. Now I've read the letter with the description of the problem. Thanks for pointing that out and providing hints for fix. I'll fix the issue soon.

---

## Comment 5

> Username: apolukhin  
> Created_at: 2015-05-10 19:06:02 UTC  
> Url: https://github.com/boostorg/variant/pull/14#issuecomment-100682027  

Fixed that in 2b2cc65.  
  
Great thanks for submitting the issue and providing fix.

---

## Comment 6

> Username: alkino  
> Created_at: 2015-05-26 17:24:30 UTC  
> Url: https://github.com/boostorg/variant/pull/14#issuecomment-105611165  

I'm sorry but it seems that it's the wrong fix.

---

## Comment 7

> Username: apolukhin  
> Created_at: 2015-05-26 20:17:30 UTC  
> Url: https://github.com/boostorg/variant/pull/14#issuecomment-105653050  

What have I missed?

---

## Comment 8

> Username: alkino  
> Created_at: 2015-05-27 00:29:14 UTC  
> Url: https://github.com/boostorg/variant/pull/14#issuecomment-105703536  

The remove_reference. remove_cv of "const int&" does nothing because it try to remove const from "int &const" and not from "int const&".

---

## Comment 9

> Username: apolukhin  
> Created_at: 2015-05-28 20:48:41 UTC  
> Url: https://github.com/boostorg/variant/pull/14#issuecomment-106591128  

Thank you for pointing that out once again. I've totally missed the `get<T&>(variant)` case in tests! The polymorphic_get<T&> and strict_get, relaxed_get versions also must be checked.  
  
I'll add the missing tests and fix the issue soon.

---

## Comment 10

> Username: apolukhin  
> Created_at: 2015-06-16 21:44:56 UTC  
> Url: https://github.com/boostorg/variant/pull/14#issuecomment-112577542  

Fixed in 34e2a9d, will be merged to master branch as soon as all the tests will cycle.

---

## Comment 11

> Username: apolukhin  
> Created_at: 2015-09-12 15:16:20 UTC  
> Url: https://github.com/boostorg/variant/pull/14#issuecomment-139783472  

Merged into 1.59 release

---
