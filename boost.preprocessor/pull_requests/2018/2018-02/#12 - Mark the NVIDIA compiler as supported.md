# #12 Mark the NVIDIA compiler as supported [Closed]

> Username: mclow  
> Created at: 2018-02-15 01:58:08 UTC  
> Updated at: 2018-02-15 07:12:10 UTC  
> Closed at: 2018-02-15 05:47:11 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/12  

See https://svn.boost.org/trac10/ticket/13447

---

## Comment 1

> Username: Flast  
> Created_at: 2018-02-15 02:14:30 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/12#issuecomment-365807068  

Newly added condition always unused because line 74 contains `defined __CUDACC__`, and then `BOOST_PP_VARIADICS` will be 0.

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-02-15 03:02:15 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/12#issuecomment-365813309  

Is __CUDACC__ ever defined when compiling with another compiler than NVidia ? I pushed a change to develop which should fix this problem assuming that __CUDACC__ was only defined by NVidia, and not by anyone else. Also the change assumes that all versions of the NVidia compiler supports variadic macros.

---

## Comment 3

> Username: Flast  
> Created_at: 2018-02-15 05:28:36 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/12#issuecomment-365829381  

AFAIK no compiler except nvcc define the symbol; I said `define"d" __CUDACC__` not `define __CUDACC__`. @eldiener your change is right.

---

## Comment 4

> Username: eldiener  
> Created_at: 2018-02-15 05:33:32 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/12#issuecomment-365829989  

OK, I intend to close this PR.

---

## Comment 5

> Username: mclow  
> Created_at: 2018-02-15 05:46:35 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/12#issuecomment-365831616  

Fine w/me.

---

## Comment 6

> Username: theZiz  
> Created_at: 2018-02-15 07:12:10 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/12#issuecomment-365843137  

Thanks a lot!

---
