# #54 - optional is "is_copy_constructible" when T is not. [Open]

> Username: biocomp  
> Created at: 2018-05-10 17:25:44 UTC  
> Updated at: 2024-03-14 06:22:49 UTC  
> Url: https://github.com/boostorg/optional/issues/54  

Hi.  
I encountered issues while trying to use std::vector<boost::optional<std::tuple<NonCopyable, std::function<...>>>. THis failed to compile on msvc.  
  
Looks like it boiled down to this repro: https://godbolt.org/g/cFNcUN  
  
```  
#include <boost/optional.hpp>  
#include <type_traits>  
#include <optional>  
  
struct NonCopyAble  
{  
    NonCopyAble() = default;  
  
    NonCopyAble(const NonCopyAble&) = delete;  
    NonCopyAble& operator=(const NonCopyAble&) = delete;  
  
    NonCopyAble(NonCopyAble&&) = default;  
    NonCopyAble& operator=(NonCopyAble&&) = default;  
};  
  
static_assert(!std::is_copy_constructible<NonCopyAble>::value, "NonCopyAble is_copy_constructible when shouldn't be");  
static_assert(!std::is_copy_constructible<std::optional<NonCopyAble>>::value, "std::optional<NonCopyAble> is_copy_constructible when shouldn't be");      
static_assert(!std::is_copy_constructible<boost::optional<NonCopyAble>>::value, "boost::optional<NonCopyAble> is_copy_constructible when shouldn't be");    // This assert fails  
```

---

## Comment 1

> Username: akrzemi1  
> Created at: 2018-05-11 07:07:43 UTC  
> Url: https://github.com/boostorg/optional/issues/54#issuecomment-388280194  

Thanks for the report. I will investigate. I acknowledge the divergence between `std::optional` and `boost::optional`. But in general the expectation on transitive detectable copiability is not valid:  
  
```c++  
static_assert(!std::is_copy_constructible<std::vector<NonCopyAble>>::value,  
              "vector of this is not copyable");  
```

---

## Comment 2

> Username: joergbrech  
> Created at: 2024-03-14 06:22:48 UTC  
> Url: https://github.com/boostorg/optional/issues/54#issuecomment-1996608341  

I encountered the same issue with `std::is_copy_assignable`, see here: https://stackoverflow.com/q/78157267/12173376
