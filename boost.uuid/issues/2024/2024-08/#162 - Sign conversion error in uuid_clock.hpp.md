# #162 - Sign conversion error in uuid_clock.hpp [Closed]

> Username: k3DW  
> Created at: 2024-08-17 19:22:12 UTC  
> Updated at: 2024-08-18 07:17:34 UTC  
> Closed at: 2024-08-18 07:17:34 UTC  
> Url: https://github.com/boostorg/uuid/issues/162  

I got this error. It's causing Unordered tests to fail that weren't previously failing.  
```  
clang-linux.compile.c++ bin.v2/libs/unordered/test/visualization_tests.test/clang-linux-12/debug/x86_64/address-sanitizer-norecover/cxxstd-17-iso/stdlib-libc++/threading-multi/undefined-sanitizer-norecover/visibility-hidden/visualization_tests.o  
In file included from libs/unordered/test/debuggability/visualization_tests.cpp:29:  
In file included from ./boost/uuid/random_generator.hpp:10:  
In file included from ./boost/uuid/basic_random_generator.hpp:10:  
In file included from ./boost/uuid/uuid.hpp:9:  
./boost/uuid/uuid_clock.hpp:66:34: error: implicit conversion changes signedness: 'std::chrono::duration<long, std::ratio<1, 10000000>>::rep' (aka 'long') to 'std::uint64_t' (aka 'unsigned long') [-Werror,-Wsign-conversion]  
    return tp.time_since_epoch().count();  
    ~~~~~~ ~~~~~~~~~~~~~~~~~~~~~~^~~~~~~  
1 error generated.  
```  
  
I see a discrepancy in the `uuid_clock` class between `int64_t` and `uint64_t`. Not sure if one of these type should be changed to be the other one, or if `static_cast` is needed.  
```  
using rep = std::int64_t;  
...  
static time_point from_timestamp( std::uint64_t timestamp ) noexcept;  
static std::uint64_t to_timestamp( time_point const& tp ) noexcept;  
```  
  
To reproduce it:  
```bash  
clang++ -Werror -Wsign-conversion path/to/boost/uuid/uuid_clock.hpp  
```
