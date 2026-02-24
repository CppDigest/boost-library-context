# #206 - Overload resolution ambiguity between std::data and boost::data on boost::span [Closed]

> Username: DiegoBaldassarMilleuno  
> Created at: 2025-10-16 14:12:18 UTC  
> Updated at: 2025-10-21 09:09:16 UTC  
> Closed at: 2025-10-19 18:56:27 UTC  
> Url: https://github.com/boostorg/core/issues/206  

The following code fails to compile (in C++17 and up) because of an ambiguous resolution between `std::data` and `boost::data` (ADL-found):  
  
```c++  
#include <boost/core/span.hpp>  
#include <iterator>  
  
int* f(boost::span<int> spn) {  
	using std::data;  
	return data(spn);  
}  
```  
  
<details>  
  <summary>Output of GCC15:</summary>  
  
```  
./test.cpp: In function 'int* f(boost::span<int>)':  
./test.cpp:19:20: error: call of overloaded 'data(boost::span<int>&)' is ambiguous  
   19 |         return data(spn);  
      |                ~~~~^~~~~  
./test.cpp:19:20: note: there are 8 candidates  
In file included from /opt/homebrew/Cellar/gcc/15.2.0/include/c++/15/array:46,  
                 from boost_1_89_0/boost/core/span.hpp:13,  
                 from ./test.cpp:1:  
/opt/homebrew/Cellar/gcc/15.2.0/include/c++/15/bits/range_access.h:335:5: note: candidate 1: 'constexpr decltype (__cont.data()) std::data(const _Container&) [with _Container = boost::span<int>; decltype (__cont.data()) = int*]'  
  335 |     data(const _Container& __cont) noexcept(noexcept(__cont.data()))  
      |     ^~~~  
/opt/homebrew/Cellar/gcc/15.2.0/include/c++/15/bits/range_access.h:324:5: note: candidate 2: 'constexpr decltype (__cont.data()) std::data(_Container&) [with _Container = boost::span<int>; decltype (__cont.data()) = int*]'  
  324 |     data(_Container& __cont) noexcept(noexcept(__cont.data()))  
      |     ^~~~  
In file included from boost_1_89_0/boost/core/span.hpp:12:  
boost_1_89_0/boost/core/data.hpp:18:1: note: candidate 3: 'constexpr decltype (c.data()) boost::data(C&) [with C =span<int>; decltype (c.data()) = int*]'  
   18 | data(C& c) noexcept(noexcept(c.data())) -> decltype(c.data())  
      | ^~~~  
boost_1_89_0/boost/core/data.hpp:25:1: note: candidate 4: 'constexpr decltype (c.data()) boost::data(const C&) [with C = span<int>; decltype (c.data()) = int*]'  
   25 | data(const C& c) noexcept(noexcept(c.data())) -> decltype(c.data())  
      | ^~~~  
```  
  
</details>  
  
  
This would not occur with `std::span`.  
Tested on Boost 1.89.0. This happens consistently with recent enough versions of GCC, clang or Apple clang.  
  
A few possible solutions:  
1. #196;  
2. Adding a couple of extra overloads to `data` for `boost::span` and `const boost::span`;  
3. Replacing the definitions for `boost::data` in `boost/core/data.hpp` only in C++17 mode and up with a single `using std::data;` in the `boost` namespace.

---

## Comment 1

> Username: Lastique  
> Created at: 2025-10-16 14:49:25 UTC  
> Url: https://github.com/boostorg/core/issues/206#issuecomment-3411290223  

I think it is worth doing all three of the proposed actions. There may be other components in namespace `boost` that support `data`. E.g. `boost::string_view` comes to mind.

---

## Comment 2

> Username: Lastique  
> Created at: 2025-10-16 15:43:13 UTC  
> Url: https://github.com/boostorg/core/issues/206#issuecomment-3411514689  

Actually, `span`-specific overloads should not be needed once `boost::data` and `boost::size` are converted to `using`-declarations. Moving `boost::span` into a deeper namespace is still worthwhile.

---

## Comment 3

> Username: Lastique  
> Created at: 2025-10-16 17:51:20 UTC  
> Url: https://github.com/boostorg/core/issues/206#issuecomment-3412093861  

@DiegoBaldassarMilleuno See if https://github.com/boostorg/core/pull/207 fixes the problem.

---

## Comment 4

> Username: DiegoBaldassarMilleuno  
> Created at: 2025-10-21 09:09:16 UTC  
> Url: https://github.com/boostorg/core/issues/206#issuecomment-3425544445  

@Lastique Sorry for the late response, just tested commit 45bd9bf from `develop` and everything works fine on my end.
