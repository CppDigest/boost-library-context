# #27 - error: cannot use builtin operator '<=>' because type 'std::strong_ordering' was not found [Closed]

> Username: mborland  
> Created at: 2025-01-28 23:16:28 UTC  
> Updated at: 2025-01-29 14:14:02 UTC  
> Closed at: 2025-01-29 14:14:00 UTC  
> Url: https://github.com/boostorg/array/issues/27  

With clangs 10-12 on ubuntu 20.04 I saw these errors today:  
  
```  
In file included from libs/decimal/test/test_boost_math_univariate_stats.cpp:46:  
In file included from ./boost/random.hpp:44:  
In file included from ./boost/random/mixmax.hpp:22:  
./boost/array.hpp:397:18: error: cannot use builtin operator '<=>' because type 'std::strong_ordering' was not found; include <compare>  
        return 0 <=> 0; // std::strong_ordering::equal  
                 ^  
./boost/array.hpp:402:24: error: cannot use builtin operator '<=>' because type 'std::strong_ordering' was not found; include <compare>  
        -> decltype( 0 <=> 0 )  
                       ^  
./boost/array.hpp:404:18: error: cannot use builtin operator '<=>' because type 'std::strong_ordering' was not found; include <compare>  
        return 0 <=> 0; // std::strong_ordering::equal  
                 ^  
3 errors generated.  
  
    "clang++-11"   -std=c++2a -fvisibility-inlines-hidden -fPIC -pthread -O0 -fno-inline -Wall -Werror -g -fvisibility=hidden -m64 --target=x86_64-pc-linux -Wconversion -Wfloat-equal -Wold-style-cast -Wsign-conversion -Wundef -DBOOST_ALL_NO_LIB=1   -I"." -I"/drone/boost-root/libs/decimal/include"  -c -o "bin.v2/libs/decimal/test/test_boost_math_univariate_stats.test/clang-linux-11/debug/x86_64/cxxstd-2a-iso/threading-multi/visibility-hidden/test_boost_math_univariate_stats.o" "libs/decimal/test/test_boost_math_univariate_stats.cpp"  
```  
See also: https://drone.cpp.al/cppalliance/decimal/1375/35/2  
  
I see the operator was added two days ago here: https://github.com/boostorg/array/commit/68db6ebd2d4813bd5dbf5e9e1c57424fdd003fde

---

## Comment 1

> Username: pdimov  
> Created at: 2025-01-29 01:10:28 UTC  
> Url: https://github.com/boostorg/array/issues/27#issuecomment-2620375394  

This configuration works for me here: https://github.com/boostorg/array/actions/runs/12996980331/job/36246949346  
  
I'll add the Clangs to Drone as well. There's probably some difference in the libstdc++ version.

---

## Comment 2

> Username: pdimov  
> Created at: 2025-01-29 03:40:56 UTC  
> Url: https://github.com/boostorg/array/issues/27#issuecomment-2620659758  

Should be fixed now.

---

## Comment 3

> Username: mborland  
> Created at: 2025-01-29 14:14:00 UTC  
> Url: https://github.com/boostorg/array/issues/27#issuecomment-2621769199  

> Should be fixed now.  
  
Confirmed. Thanks for the quick fix.
