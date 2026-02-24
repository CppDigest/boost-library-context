# #19 - error/warning: left shift of negative value [Closed]

> Username: olk  
> Created at: 2018-01-31 06:33:39 UTC  
> Updated at: 2018-02-01 16:47:29 UTC  
> Closed at: 2018-01-31 19:11:57 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/19  

compiling unit-tests with gcc -W -Wall -Werror produces errors like:  
  
../../../boost/dynamic_bitset/dynamic_bitset.hpp:1953:57: error: left shift of negative value [-Werror=shift-negative-value]  
block_type const mask = (~static_cast(0) << extra_bits);

---

## Comment 1

> Username: mclow  
> Created at: 2018-01-31 16:32:36 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/19#issuecomment-361989216  

Since I complained about your proposed fix, I'll take a look at this.

---

## Comment 2

> Username: mclow  
> Created at: 2018-01-31 17:57:11 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/19#issuecomment-362016448  

I'm not getting these warnings with clang.  I'll try with gcc.

---

## Comment 3

> Username: olk  
> Created at: 2018-01-31 18:00:29 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/19#issuecomment-362017522  

b2 toolset=gcc cxxflags="-std=c++17 -W -Wall -Werror" -j 32  
  
dyn_bitset_unit_tests2.cpp:285:33:   von hier erfordert  
../../../boost/dynamic_bitset/dynamic_bitset.hpp:1953:57: Fehler: left shift of negative value [-Werror=shift-negative-value]  
         block_type const mask = (~static_cast<Block>(0) << extra_bits);  
  
  
gcc -v: gcc-Version 7.2.1

---

## Comment 4

> Username: mclow  
> Created at: 2018-01-31 18:01:37 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/19#issuecomment-362017839  

Must be a new warning with 7.2; I don't get them with 7.1.  
/me Off to install a newer version....

---

## Comment 5

> Username: olk  
> Created at: 2018-01-31 18:02:44 UTC  
> Updated at: 2018-01-31 18:03:32 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/19#issuecomment-362018168  

got this with gcc-6.3.0 too using the develop branch

---

## Comment 6

> Username: mclow  
> Created at: 2018-01-31 18:20:56 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/19#issuecomment-362023459  

Ok - `c++17` is the key.

---

## Comment 7

> Username: olk  
> Created at: 2018-01-31 18:56:51 UTC  
> Updated at: 2018-01-31 18:57:01 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/19#issuecomment-362033894  

`b2 toolset=gcc cxxflags="-std=c++11 -W -Wall -Werror" -j 32` produces the same error (Arch Linux, gcc-7.2.1)

---

## Comment 8

> Username: mclow  
> Created at: 2018-01-31 19:11:57 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/19#issuecomment-362038476  

Fixed in d3eb4faf0cc0d7a7f0492fc60f490c95066282bf

---

## Comment 9

> Username: olk  
> Created at: 2018-01-31 19:23:25 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/19#issuecomment-362041784  

fix works, thx

---

## Comment 10

> Username: igorsugak  
> Created at: 2018-02-01 16:47:29 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/19#issuecomment-362327202  

I just discovered this same bug with UBSAN:  
`dynamic_bitset.hpp:1943:55: runtime error: left shift of negative value -1`  
Thank you for fixing this!
