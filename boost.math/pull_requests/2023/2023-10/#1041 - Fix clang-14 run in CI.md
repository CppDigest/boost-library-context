# #1041 Fix clang-14 run in CI [Merged]

> Username: mborland  
> Created at: 2023-10-31 07:13:21 UTC  
> Updated at: 2023-10-31 10:33:40 UTC  
> Merged at: 2023-10-31 10:33:36 UTC  
> Closed at: 2023-10-31 10:33:37 UTC  
> Url: https://github.com/boostorg/math/pull/1041  

The updated GHA runners broke Clang-14 in C++20 mode. G++12 in C++20 mode is covered in our drone runs which are fine..

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-10-31 09:21:58 UTC  
> Url: https://github.com/boostorg/math/pull/1041#issuecomment-1786819609  

Just out of curiosity, what was the breakage, it seems a shame we can't run clang in C++20.

---

## Comment 2

> Username: mborland  
> Created_at: 2023-10-31 09:27:23 UTC  
> Url: https://github.com/boostorg/math/pull/1041#issuecomment-1786828678  

> Just out of curiosity, what was the breakage, it seems a shame we can't run clang in C++20.  
  
There are two to watch for now. This will hit any Clang < 16 in 2b mode:  
  
```  
/usr/bin/../lib/gcc/x86_64-linux-gnu/13/../../../../include/c++/13/bits/stl_iterator.h:2618:35: error: missing 'typename' prior to dependent type name 'iterator_traits<_It>::iterator_category'  
      { using iterator_category = iterator_traits<_It>::iterator_category; };  
                                  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
  
And this will happen in C++20 mode:  
  
```  
/usr/bin/../lib/gcc/x86_64-linux-gnu/13/../../../../include/c++/13/chrono:2320:48: error: call to consteval function 'std::chrono::hh_mm_ss::_S_fractional_width' is not a constant expression  
        static constexpr unsigned fractional_width = {_S_fractional_width()};  
                                                      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/13/../../../../include/c++/13/chrono:2320:48: note: undefined function '_S_fractional_width' cannot be used in a constant expression  
/usr/bin/../lib/gcc/x86_64-linux-gnu/13/../../../../include/c++/13/chrono:2275:2: note: declared here  
        _S_fractional_width()  
        ^  
1 error generated.  
```  
  
The GHA 22.04 image updated from GCC12 to 13 as the default version a few days ago.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2023-10-31 09:48:22 UTC  
> Updated_at: 2023-10-31 09:49:03 UTC  
> Url: https://github.com/boostorg/math/pull/1041#issuecomment-1786867098  

> The GHA 22.04 image updated from GCC12 to 13 as the default version a few days ago.  
  
This seemingly harmless change actually broke parts of `iterator_traits` and parts of `chrono`. I spent all weekend working around in unrelated other opesn-source projects in order to keep those compilers. But you need to have super-simple uses of iterators and chrono-functions. I don't think Boost will have such simplistic cases...  
  
Tough break on this one.

---
