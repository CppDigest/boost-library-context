# #37 - Potential footgun in `ptr_map` [Open]

> Username: Noxitu  
> Created at: 2023-03-06 23:32:16 UTC  
> Updated at: 2023-03-07 18:48:11 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/37  

There is an easy risk of creating use-after-free errors with current implementation of iterators. This simple code:  
  
```c++  
auto it = map.find(key);  
const auto &data = it->second;  
```  
  
The problem comes from `iterator::operator->` returning temporary (`proxy`), where `second` is a value, and not reference. This means that as soon as the assignment to `data` ends, temporary is freed and our reference points to destroyed object. This will be also true for directly doing `map.find(key)->second`.  
  
A full example on godbolt with enabled address sanitizer: https://godbolt.org/z/M9h43Pdd1  
  
This problem is dangerous, because it doesn't generate any warning even with decently aggressive flags (on clang-15, `-Wall -Wextra -Wpedantic`). Moreover, same code for `std::map` works correctly.  
  
I have no idea if this can be solved on the code level (i.e. if there is a value that could be referenced in that proxy instead of making a copy), or if only solution is just making sure documentation warns about this.
