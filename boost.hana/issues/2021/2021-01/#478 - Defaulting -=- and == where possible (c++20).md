# #478 - Defaulting <=> and == where possible (c++20) [Open]

> Username: rgreenblatt  
> Created at: 2021-01-16 21:12:57 UTC  
> Updated at: 2021-01-19 21:45:12 UTC  
> Url: https://github.com/boostorg/hana/issues/478  

If there was a three_way_compare.hpp this would reduce compile times and the number of distinct headers which must be included for hana types to be totally ordered (where applicable).   
  
This also could make it possible to default <=> for types that contain hana::tuple.

---

## Comment 1

> Username: rgreenblatt  
> Created at: 2021-01-17 17:51:41 UTC  
> Updated at: 2021-01-17 17:54:51 UTC  
> Url: https://github.com/boostorg/hana/issues/478#issuecomment-761851578  

Defaulting operator== can make constexpr functions much faster.  
For example, consider the following compile time benchmark program:  
```cpp  
#include <boost/hana/tuple.hpp>  
#include <boost/hana/equal.hpp>  
#include <boost/hana/not_equal.hpp>  
#include <boost/hana/less.hpp>  
  
#include <array>  
#include <algorithm>  
  
namespace hana = boost::hana;  
  
static constexpr std::size_t inner_size = 1<<6;  
static constexpr std::size_t outer_size = 1<<6;  
  
constexpr unsigned fnv_hash(unsigned in) {  
  constexpr unsigned prime = 16777619;  
  constexpr unsigned offset_basis = 2166136261;  
  unsigned out_hash = offset_basis;  
  for (unsigned i = 0; i < 4; ++i) {  
    out_hash ^= (in >> (8 * i)) & 0xff;  
    out_hash *= prime;  
  }  
  return out_hash;  
}  
  
constexpr auto values = [] {  
  std::array<std::array<hana::tuple<std::size_t>, inner_size>, outer_size> arr;  
  for (std::size_t i = 0; i < outer_size; ++i) {  
    for (std::size_t j = 0; j < inner_size; ++j) {  
      arr[i][j] = {fnv_hash(j) ^ fnv_hash(i)};  
    }  
  }  
  
  return arr;  
}();  
  
template <std::equality_comparable T, std::size_t inner_size,  
          std::size_t outer_size>  
          requires requires (const T& t) {  
            { t < t } -> std::convertible_to<bool>;  
          }  
consteval bool all_values_unique(  
    std::array<std::array<T, inner_size>, outer_size>  
        values) {  
  for (std::size_t i = 0; i < values.size(); ++i) {  
    auto& sub_values = values[i];  
#ifdef SORT_IMPL  
    std::sort(sub_values.begin(), sub_values.end());  
    auto it = std::unique(sub_values.begin(), sub_values.end());  
    if(it != sub_values.end()) {  
      return false;  
    }  
#else  
    for (std::size_t j = 0; j < sub_values.size(); ++j) {  
      for (std::size_t k = 0; k < j; ++k) {  
        if (sub_values[j] == sub_values[k]) {  
          return false;  
        }  
      }  
    }  
#endif  
  }  
  
  return true;  
}  
  
// probably all unique for small sizes...  
static_assert(all_values_unique(values));  
```  
I ran some hacked together benchmarks on my system with hyperfine.  
When built with clang++ -std=c++20 -fsyntax-only -fconstexpr-steps=10000000, this takes 6.596 s ±  0.071 s  
With -DSORT_IMPL, it only takes 2.543 s ±  0.032 s.  
If I hack in defaulted operator== and operator<=> the naive implementation gets much faster and the -DSORT_IMPL doesn't change much (maybe slightly faster).  
The naive implementation now takes only 2.696 s ±  0.058 s and the -DSORT_IMPL takes 2.441 s ±  0.038 s.  
  
My branch with hacked in defaulted operator== and operator<=> for just tuple is here: https://github.com/rgreenblatt/hana/tree/hacky_default_compare  
  
Unfortunately, the static assert fails on hacked in operator== with gcc. This seems like a gcc bug. I will look into it later.

---

## Comment 2

> Username: rgreenblatt  
> Created at: 2021-01-17 20:21:00 UTC  
> Url: https://github.com/boostorg/hana/issues/478#issuecomment-761873440  

gcc bug: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=98712

---

## Comment 3

> Username: ricejasonf  
> Created at: 2021-01-19 18:25:43 UTC  
> Url: https://github.com/boostorg/hana/issues/478#issuecomment-763033966  

The operators in Boost.Hana alias function objects like `hana::equal` and `hana::less` which in turn call specializations of `hana::equal_impl<T>` and `hana::less_impl<T>`. Defining operators directly in the class would certainly bypass all of this making it compile faster for those who use the operators. I'm not sure how much it would affect the use of the function objects to have them rely on the operators if they were implemented for types like `tuple`.  
   
 Is everything working with your changes? Do all of the tests pass? (ie `make check`)  
  
For compile-time benchmarking, I highly recommend https://github.com/ldionne/metabench.

---

## Comment 4

> Username: rgreenblatt  
> Created at: 2021-01-19 20:26:07 UTC  
> Updated at: 2021-01-19 20:27:20 UTC  
> Url: https://github.com/boostorg/hana/issues/478#issuecomment-763110465  

> Is everything working with your changes? Do all of the tests pass? (ie `make check`)  
  
Nope.   
  
It can't build on clang because clang doesn't have [P2002R1](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2020/p2002r1.html) fully implemented which allows for operator{==,<=>} to be declared without constexpr and deduce constexpr. (I was originally testing using just clang and with all of the operators declared constexpr - this breaks if any of the tuple types don't have constexpr compare)  
  
On gcc it should theoretically work with all of the operator{==,<=>} not declared constexpr (except the ones which are always constexpr), but there are a few issues:  
 - hana appears to make comparisions return compile time constants when possible.   
   For example: `decltype(hana::tuple<>{} == hana::tuple<>{})::value` works. As far as I know, there is no way to get this behavior with defaulted operators. This makes a bunch of tests and examples fail to compile because they use `BOOST_HANA_CONSTANT_CHECK`. There is probably a nice way to work around this by disabling the operators whenever a type can be  equality checked at compile time via `requires`, but I'm not going to figure this out right now.  
 - If a type inherits from two different types which default operator{==,<=>} but it doesn't declare them itself, this results in a `request for member ... is ambiguous`. This can be solved by putting operator{==,<=>} everywhere (rather than just the things needed for tuple).  
 - The gcc bug I mentioned earlier _might_ cause issues with the few that need to be declared constexpr - I don't think this is an issue because these few structs don't have any state, but I haven't really tested anything.

---

## Comment 5

> Username: ricejasonf  
> Created at: 2021-01-19 21:44:02 UTC  
> Updated at: 2021-01-19 21:45:12 UTC  
> Url: https://github.com/boostorg/hana/issues/478#issuecomment-763160719  

> hana appears to make comparisions return compile time constants when possible  
  
They must always return an object wrapping a constexpr bool convertible to bool.  
  
There is also the complication that an implementation  of `hana::equal` exists for `hana::Sequence` allowing comparison across different tuple-like types. This also works with the operators which are really just sugar:  
  
```cpp  
#include <boost/hana/equal.hpp>  
#include <boost/hana/tuple.hpp>  
#include <boost/hana/ext/std/tuple.hpp>  
  
namespace hana = boost::hana;  
  
static_assert(hana::tuple(1, 2, 3) == std::tuple(1, 2, 3));  
```
