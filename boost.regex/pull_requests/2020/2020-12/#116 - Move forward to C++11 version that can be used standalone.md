# #116 Move forward to C++11 version that can be used standalone. [Merged]

> Username: jzmaddock  
> Created at: 2020-12-10 20:11:34 UTC  
> Updated at: 2022-03-19 16:30:01 UTC  
> Merged at: 2021-01-25 13:50:13 UTC  
> Closed at: 2021-01-25 13:50:13 UTC  
> Url: https://github.com/boostorg/regex/pull/116  



---

## Comment 1

> Username: Mike-Devel  
> Created_at: 2020-12-14 22:15:59 UTC  
> Url: https://github.com/boostorg/regex/pull/116#issuecomment-744744616  

Please give a heads up, when this is ready to test.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-12-26 18:48:15 UTC  
> Url: https://github.com/boostorg/regex/pull/116#issuecomment-751385020  

@Mike-Devel : This is good to go bar docs, so now would be a good time to test things out.  Standalone mode is activated either by defining BOOST_REGEX_STANDALONE, or by just not having boost in your include path as long as the compiler supports __has_include.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2021-01-26 20:53:19 UTC  
> Url: https://github.com/boostorg/regex/pull/116#issuecomment-767820738  

This broke Spirit as it uses `boost/regex/pending/unicode_iterator.hpp`, what should I use instead?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-01-26 22:32:58 UTC  
> Url: https://github.com/boostorg/regex/pull/116#issuecomment-767871289  

> This broke Spirit as it uses boost/regex/pending/unicode_iterator.hpp, what should I use instead?  
  
Oh :(  I'll add a forwarding header tomorrow.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-01-27 12:39:03 UTC  
> Url: https://github.com/boostorg/regex/pull/116#issuecomment-768258349  

> This broke Spirit as it uses boost/regex/pending/unicode_iterator.hpp, what should I use instead?  
  
Header reinstated.  Thanks for spotting that!

---

## Comment 6

> Username: Kojoley  
> Created_at: 2021-01-27 15:35:31 UTC  
> Url: https://github.com/boostorg/regex/pull/116#issuecomment-768369132  

>Header reinstated. Thanks for spotting that!  
  
Thanks!

---
