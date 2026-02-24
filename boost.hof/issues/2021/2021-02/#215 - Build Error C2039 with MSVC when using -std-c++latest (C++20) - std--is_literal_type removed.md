# #215 - Build Error C2039 with MSVC when using /std:c++latest (C++20) - std::is_literal_type removed [Open]

> Username: pumkinpal  
> Created at: 2021-02-01 13:29:15 UTC  
> Updated at: 2021-04-12 17:28:06 UTC  
> Url: https://github.com/boostorg/hof/issues/215  

The following minimum sample fails to compile when using MSVC with /std:c++latest (C++20).  
See: [godbolt example](https://godbolt.org/z/dEcGEs)  
  
```  
#include <vector>  
#include <algorithm>  
#include <boost/hof/proj.hpp>  
#include <boost/hof/placeholders.hpp>  
  
struct A  
{  
    int i;  
    char c;  
};  
  
void foo(std::vector<A>& vec)  
{  
    using namespace boost::hof;  
    std::sort(vec.begin(), vec.end(), proj(&A::i, _ < _));  
}  
```  
The main issue is that HOF uses std::is_literal_type which was deprecated in C++17 and removed in C++20 and the Microsoft STL has removed it ([link](https://github.com/bolero-MURAKAMI/Sprout/issues/95))

---

## Comment 1

> Username: pfultz2  
> Created at: 2021-02-01 18:56:54 UTC  
> Url: https://github.com/boostorg/hof/issues/215#issuecomment-771080158  

So it looks like `__is_literal_type` intrinsic is still available, so I should use that instead.

---

## Comment 2

> Username: Tradias  
> Created at: 2021-04-12 17:28:06 UTC  
> Url: https://github.com/boostorg/hof/issues/215#issuecomment-817991261  

@pfultz2 any chance that this makes it into Boost 1.76.0?
