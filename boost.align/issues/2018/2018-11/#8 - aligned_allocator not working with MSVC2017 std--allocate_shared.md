# #8 - aligned_allocator not working with MSVC2017 std::allocate_shared [Closed]

> Username: JVApen  
> Created at: 2018-11-25 20:41:55 UTC  
> Updated at: 2018-11-25 21:09:42 UTC  
> Closed at: 2018-11-25 21:09:42 UTC  
> Url: https://github.com/boostorg/align/issues/8  

Reproduction on Compiler Explorer: https://gcc.godbolt.org/z/kJuafk  
  
```  
#include <boost/align/aligned_allocator.hpp>  
#include <type_traits>  
  
struct alignas(64) C  
{};  
  
template<typename T>  
using Allocator = boost::alignment::aligned_allocator<T, std::alignment_of_v<T>>;  
  
auto f()  
{  
      auto a = Allocator<C>{};  
      return std::allocate_shared<C>(a);  
}  
  
```  
  
Error: error C2139: 'std::_Wrap_alloc<boost::alignment::aligned_allocator<U,64>>': an undefined class is not allowed as an argument to compiler intrinsic type trait '__is_empty'

---

## Comment 1

> Username: glenfe  
> Created at: 2018-11-25 21:09:42 UTC  
> Url: https://github.com/boostorg/align/issues/8#issuecomment-441473487  

Thank you for reporting this. It looks like MSVC's allocate_shared implementation rebinds the Allocator to a value type that is incomplete, which instantiates the template with that incomplete type. Fixed in 477668e.
