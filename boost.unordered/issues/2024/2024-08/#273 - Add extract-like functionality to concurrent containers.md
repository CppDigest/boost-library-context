# #273 - [FR] Add extract-like functionality to concurrent containers [Closed]

> Username: demozon  
> Created at: 2024-08-15 13:04:53 UTC  
> Updated at: 2024-08-21 08:58:12 UTC  
> Closed at: 2024-08-21 08:58:12 UTC  
> Url: https://github.com/boostorg/unordered/issues/273  

Hi,  
  
Is it feasible to add a function for extracting elements (perhaps only by key) that takes a function as an additional argument?  
Or perhaps add an additional overload to erase?  
  
E.g. something like  
  
template<class F>  
void extract(const key_type& k, F f);  
  
My usecase is that I have an issue where I have an object that tries to delete itself.

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-08-16 11:32:48 UTC  
> Updated at: 2024-08-16 11:35:50 UTC  
> Url: https://github.com/boostorg/unordered/issues/273#issuecomment-2293340009  

You can "extract" elements [as follows](https://godbolt.org/z/v41T9jsaj):  
```cpp  
#include <boost/unordered/concurrent_flat_map.hpp>  
#include <cassert>  
#include <optional>  
  
int main()  
{  
  using map = boost::concurrent_flat_map<int, int>;  
  using value_type = map::value_type;  
    
  map m = {{1, 2}};  
  std::optional<value_type> o;  
  m.erase_if(1, [&](value_type& x) {  
    o.emplace(std::move(x));   
    return true;  
  });  
  assert(o->first == 1);  
  assert(o->second == 2);  
}  
```  
Extraction proper (without incurring any move construction) is not possible as `boost::concurrent_flat_(map|set)` are not node-based. In Boost 1.87, `boost::concurrent_node_(map|set)` will be provided with actual `extract` capabilities, but bear in mind that these containers are generally slower than their flat counterparts.

---

## Comment 2

> Username: demozon  
> Created at: 2024-08-21 08:57:34 UTC  
> Url: https://github.com/boostorg/unordered/issues/273#issuecomment-2301527743  

That does do the trick, actually. Just need a const_cast in my case, since I'm using a set.

---

## Comment 3

> Username: demozon  
> Created at: 2024-08-21 08:58:12 UTC  
> Url: https://github.com/boostorg/unordered/issues/273#issuecomment-2301529053  

There exists a workaround
