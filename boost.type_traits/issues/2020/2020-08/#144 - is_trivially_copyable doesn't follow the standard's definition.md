# #144 - is_trivially_copyable doesn't follow the standard's definition [Open]

> Username: sarah-quinones  
> Created at: 2020-08-14 09:49:30 UTC  
> Updated at: 2020-08-14 15:34:30 UTC  
> Url: https://github.com/boostorg/type_traits/issues/144  

```cpp  
#include <boost/type_traits.hpp>  
#include <type_traits>  
  
struct foo {  
  foo(foo const&) = delete; // deleted copy constructor  
  foo& operator=(foo const&) = default;  
};  
  
struct bar {  
  bar(bar const&) = default;  
  bar(bar &&) {} // user defined move constructor  
  auto operator=(bar const&) -> bar& = default;  
  auto operator=(bar &&    ) -> bar& { return *this; } // user defined move assignment  
};  
  
static_assert(not boost::is_trivially_copyable<foo>::value);  
static_assert(    std  ::is_trivially_copyable<foo>::value);  
  
static_assert(    boost::is_trivially_copyable<bar>::value);  
static_assert(not std  ::is_trivially_copyable<bar>::value);  
```  
  
https://en.cppreference.com/w/cpp/named_req/TriviallyCopyable  
types with deleted constructors/move assignment operators are allowed to be trivially copyable, as long as at least one of them is defined, and all the non deleted ones are trivial.

---

## Comment 1

> Username: glenfe  
> Created at: 2020-08-14 13:28:16 UTC  
> Url: https://github.com/boostorg/type_traits/issues/144#issuecomment-674074692  

Thanks; I'll update the implementation for 1.75. The current definition is better than `boost::has_trivial_copy` for deciding whether or not to use `memcpy`, but it isn't perfect.

---

## Comment 2

> Username: glenfe  
> Created at: 2020-08-14 15:34:30 UTC  
> Url: https://github.com/boostorg/type_traits/issues/144#issuecomment-674132455  

The new implementation will prefer an intrinsic for IsTriviallyCopyable if one is available, the fallback will be what we have in develop now (false negatives being safer than false positives).
