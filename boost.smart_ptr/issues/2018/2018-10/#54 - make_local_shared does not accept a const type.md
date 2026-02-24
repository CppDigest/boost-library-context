# #54 - make_local_shared does not accept a const type [Closed]

> Username: dvd0101  
> Created at: 2018-10-12 20:58:36 UTC  
> Updated at: 2018-11-10 23:02:18 UTC  
> Closed at: 2018-11-10 23:02:17 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/54  

Differently from std::make_shared and boost::make_shared, boost::make_local_shared does not accept a const type.  
  
```cpp  
#include <boost/smart_ptr/make_local_shared.hpp>  
  
auto h() {  
    return boost::make_local_shared<const int>();  
}  
```  
  
This snippet fails to compile because make_local_shared tries to construct a std::allocator<const inst>.  
  
You can check it online on godbolt: https://gcc.godbolt.org/z/ghQWbM

---

## Comment 1

> Username: pdimov  
> Created at: 2018-11-10 23:02:17 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/54#issuecomment-437628508  

Fixed in https://github.com/boostorg/smart_ptr/commit/f6c3508aeed7d6f3838015713c480090371e154d. Sorry for the delay.
