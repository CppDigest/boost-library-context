# #193 Remove auto_ptr (deprecated in recent C++ standards). [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2016-05-31 12:23:58 UTC  
> Updated at: 2017-08-07 05:54:06 UTC  
> Merged at: 2016-05-31 14:23:11 UTC  
> Closed at: 2016-05-31 14:23:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/193  

Hi,  
  
I am having issues when using json_spirit and boost 1.61 with GCC 6 and -std=gnu++14 -Wall -Werror -Wextra.  
  
This removes all the last auto_ptr references in boost spirit. Inspired by https://svn.boost.org/trac/boost/ticket/11672.  
  
I have tested with json_spirit and boost 1.61, then ported to the branch "develop" as is.  
  
Cheers,  
Romain

---

## Comment 1

> Username: prlw1  
> Created_at: 2016-06-08 11:19:18 UTC  
> Updated_at: 2016-06-08 11:19:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/193#issuecomment-224560417  

Question from the peanut gallery: why not go for std::unique_ptr from &lt;memory&gt; rather than boost::movelib::unique_ptr ?

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2016-06-08 11:49:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/193#issuecomment-224566264  

Most of boost library are meant to work on a wide range of platform/compilers/C++ standards. A non neglibeagle part of people in the industry still use C++03 (Amadeus which I am working for is currently massively migrating to C++11, and I am preparing the next move to C++14). AFAIK most the current Boost libraries currently supports that (using C++03). There are some exceptions like Boost Hana for example, but not a lot.  
  
So I could have used a #ifdef to conditionally use std::unique_ptr when available, but for me adding a lot of #ifdef bloats the code. On the other hand, boost::movelib::unique_ptr is available no matter what configuration you use.

---

## Comment 3

> Username: djowel  
> Created_at: 2016-06-08 22:14:56 UTC  
> Updated_at: 2016-06-08 22:16:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/193#issuecomment-224746520  

That is not necessary. Spirit Classic is meant to work for older compilers as a backward compatible support for legacy Spirit code. For that, boost::movelib::unique_ptr is the right choice.

---

## Comment 4

> Username: prlw1  
> Created_at: 2016-06-11 08:27:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/193#issuecomment-225344271  

Thanks for the explanation! (I missed the obvious - not all the world is x3)

---

## Comment 5

> Username: djowel  
> Created_at: 2017-08-07 03:07:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/193#issuecomment-320558244  

My bad. I'll do so as soon as possible.

---

## Comment 6

> Username: djowel  
> Created_at: 2017-08-07 03:15:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/193#issuecomment-320559012  

merged to master

---

## Comment 7

> Username: Romain-Geissler-1A  
> Created_at: 2017-08-07 05:54:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/193#issuecomment-320575005  

Thank you. Is this too late to be included in Boost 1.65 ? Or will it be in 1.66 ?

---
