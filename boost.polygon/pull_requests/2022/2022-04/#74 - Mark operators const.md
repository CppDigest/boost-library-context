# #74 Mark operators const [Merged]

> Username: fiesh  
> Created at: 2022-04-11 19:32:58 UTC  
> Updated at: 2023-08-30 04:48:45 UTC  
> Merged at: 2023-08-30 04:48:45 UTC  
> Closed at: 2023-08-30 04:48:45 UTC  
> Url: https://github.com/boostorg/polygon/pull/74  

These operators missing the `const` keyword made them incorrect code in C++20:  
  
Let `t.cpp` be the following file:  
  
```cpp  
#include <polygon.hpp>  
using T = boost::polygon::poly_line_arbitrary_polygon_data<int>::iterator_holes_type;                                                                                           
bool f() { return T{} != T{}; }           
```  
  
Then with `clang-13`:  
  
```sh  
% clang++ -std=c++20 -I polygon/include/boost/polygon -c t.cpp  
t.cpp:6:23: warning: ISO C++20 considers use of overloaded operator '!=' (with operand types 'T' (aka 'boost::polygon::poly_line_arbitrary_polygon_data<int>::iterator_holes_type') and 'T') to be ambiguous despite there being a unique best viable function with non-reversed arguments [-Wambiguous-reversed-operator]  
bool f() { return T{} != T{}; }  
                  ~~~ ^  ~~~  
polygon/include/boost/polygon/detail/polygon_arbitrary_formation.hpp:2179:19: note: candidate function with non-reversed arguments  
      inline bool operator!=(const iterator_holes_type& that) { return itr_ != that.itr_; }  
                  ^  
polygon/include/boost/polygon/detail/polygon_arbitrary_formation.hpp:2178:19: note: ambiguous candidate function with reversed arguments  
      inline bool operator==(const iterator_holes_type& that) { return itr_ == that.itr_; }  
                  ^  
1 warning generated.  
```  
  
Making the operators `const` resolves this issue.

---

## Comment 1

> Username: fiesh  
> Created_at: 2022-04-12 12:17:31 UTC  
> Url: https://github.com/boostorg/polygon/pull/74#issuecomment-1096651207  

This in particular fixes `polygon_set`'s `resize` and `shrink` operations with C++20 which don't compile otherwise. (With `clang` at least.)

---

## Comment 2

> Username: fiesh  
> Created_at: 2022-04-12 12:24:54 UTC  
> Url: https://github.com/boostorg/polygon/pull/74#issuecomment-1096658638  

@asydorchuk I know this library is basically dead, but including such a simple pull request would be great since otherwise it is incompatible with C++20, a fact that just isn't caught by any test so far I suppose.

---
