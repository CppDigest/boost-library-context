# #370 - GCD fails on cpp_int [Closed]

> Username: danston  
> Created at: 2021-09-20 10:59:58 UTC  
> Updated at: 2021-09-29 17:04:14 UTC  
> Closed at: 2021-09-29 17:04:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/370  

I am having an issue with calling `gcd` on certain `cpp_int` numbers for several examples in CGAL. I have created a minimal example [here](https://gist.github.com/danston/f204af166b06b7fbe2c6fc64d660a335).  
  
I have tested it on `macOS x86_64` and `Linux` platforms (both `clang` and `gcc`) and, in both cases, the `gcd` runs forever. To be more precise, in `misc.hpp`  inside `eval_gcd_lehmer` there is a call to `divide_subtract` where we enter a while loop but we never exit this loop. After some checking, it seems like the issue was introduced somewhere in-between versions 1.75 and 1.76 of boost because 1.75 works correctly while 1.76 fails. The commit  
  
```  
commit 4f8e0055acc1ef48a2845202a7d87a5ab712c0f8  
Author: jzmaddock <john@johnmaddock.co.uk>  
Date:   Wed Jul 15 18:15:59 2020 +0100  
  
    GCD: Add double digit Lehmer's algorithm for platforms with synthetic double_limb_type.  
  
 include/boost/multiprecision/cpp_int/misc.hpp | 337 ++++++++++++++++----------  
 test/Jamfile.v2                               |   2 +-  
 test/test_gcd.cpp                             | 118 +++++++++  
 3 files changed, 322 insertions(+), 135 deletions(-)  
 create mode 100644 test/test_gcd.cpp  
```  
  
seems to introduce this issue. With the previous commit `981aee54dc2e1aec2d752bb188d9bf6f9d4af0a3` it works correctly.  
  
It would be nice if someone from the authors could have a look and tell me if it is indeed a bug. Thank you!

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-09-20 12:21:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/370#issuecomment-922879091  

Anything that goes into an infinite loop is most certainly a bug.  Will look into it shortly.

---

## Comment 2

> Username: afabri  
> Created at: 2021-09-25 15:05:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/370#issuecomment-927134005  

Hi @jzmaddock. Note that I could not reproduce the bug with VC++ on Windows. Just in case this was your platform.  To me this seems  a serious bug, as the gcd is computed several times in each arithmetic operation.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-09-25 18:45:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/370#issuecomment-927166472  

Confirmed, I just don't see the bug yet.
