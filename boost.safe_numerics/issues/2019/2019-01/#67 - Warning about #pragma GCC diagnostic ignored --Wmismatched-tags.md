# #67 - Warning about #pragma GCC diagnostic ignored "-Wmismatched-tags" [Closed]

> Username: Mike-Devel  
> Created at: 2019-01-05 08:32:46 UTC  
> Updated at: 2019-08-27 16:11:43 UTC  
> Closed at: 2019-08-27 16:11:43 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/67  

When compiling the library from develop branch with cmake and gcc 7.3 I get the following warning:  
  
    In file included from <boost_root>/libs/safe_numerics/include/boost/safe_numerics/safe_integer.hpp:14:0,  
                     from <boost_root>/libs/safe_numerics/example/example92.cpp:16:  
    <boost_root>/libs/safe_numerics/include/boost/safe_numerics/safe_base.hpp:103:32: warning: unknown option after ‘#pragma GCC diagnostic’ kind [-Wpragmas]  
     #pragma GCC diagnostic ignored "-Wmismatched-tags"  
  
From what little I could find on the internet, gcc doesn't support `-Wmismatched-tags` in the first place (https://gcc.gnu.org/wiki/FAQ#Why_GCC_does_not_warn_for_mismatch_between_struct_and_class_.28-Wmismatched-tags.29_.3F). If this is supposed to silince a warning in clang, `#pragma clang diagnostic ignored "-Wmismatched-tags"` seems more appropriate, but I currently don't have a setup to check this with different compiler versions.

---

## Comment 1

> Username: robertramey  
> Created at: 2019-01-05 16:36:42 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/67#issuecomment-451669767  

I've spent this time on this previously.  I only added the pragma when I was getting a ton  of annoying warnings on gcc compilers.  I put this in an it eliminated the first set of warnings but left me with a new one which I have been unable to suppress.  I'd love to see a solution to this.

---

## Comment 2

> Username: Mike-Devel  
> Created at: 2019-01-05 21:56:19 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/67#issuecomment-451694102  

What warnings did you get originally? I just removed it and didn't get any warnings in gcc 7.3.

---

## Comment 3

> Username: robertramey  
> Created at: 2019-01-05 22:09:21 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/67#issuecomment-451695257  

mismatched class vs struct

---

## Comment 4

> Username: robertramey  
> Created at: 2019-01-05 22:09:45 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/67#issuecomment-451695296  

from the standard library

---

## Comment 5

> Username: Mike-Devel  
> Created at: 2019-01-05 23:06:33 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/67#issuecomment-451699899  

Seems I managed to confuse myself. I got this warning not from develop, but from my feature branch (the one from https://github.com/boostorg/safe_numerics/pull/68). Will investigate further and report back. Sorry for the noise.
