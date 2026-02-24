# #102 SFINAE-friendly fusion::fold [Merged]

> Username: Flast  
> Created at: 2015-09-07 15:47:00 UTC  
> Updated at: 2015-10-17 02:05:45 UTC  
> Merged at: 2015-10-15 22:32:12 UTC  
> Closed at: 2015-10-15 22:32:12 UTC  
> Url: https://github.com/boostorg/fusion/pull/102  

I overhauled fusion::fold to become SFINAE-friendly; It removes `result_of` hack (i.e. `result_of_with_decltype`).  
  
Also, this PR will resolve [#11606](https://svn.boost.org/trac/boost/ticket/11606).  
  
Tested under GCC 5.1.1, Clang 3.6.2, MSVC 9,10,11 and 12 .

---

## Comment 1

> Username: djowel  
> Created_at: 2015-09-07 18:05:15 UTC  
> Url: https://github.com/boostorg/fusion/pull/102#issuecomment-138350427  

This is very welcome news! I'll go review the code ASAP.

---

## Comment 2

> Username: Flast  
> Created_at: 2015-10-15 05:37:53 UTC  
> Url: https://github.com/boostorg/fusion/pull/102#issuecomment-148286662  

What's the current status? Did I miss something?

---

## Comment 3

> Username: djowel  
> Created_at: 2015-10-15 22:32:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/102#issuecomment-148541814  

Ok, let's bite the bullet and go for this.

---

## Comment 4

> Username: Lastique  
> Created_at: 2015-10-16 12:48:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/102#issuecomment-148707837  

I think merging this PR broke develop recently as it removes |result_of_with_decltype| but not updates preprocessed headers, which still use it. See the errors here:  
  
http://www.boost.org/development/tests/develop/developer/output/teeks99-01b-Ubuntu14-04-64-boost-bin-v2-libs-log-test-attr_attribute_set-test-clang-linux-3-6-debug-threading-multi.html

---
