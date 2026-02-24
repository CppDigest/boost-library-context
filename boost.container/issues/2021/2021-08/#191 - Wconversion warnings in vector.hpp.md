# #191 - Wconversion warnings in vector.hpp [Closed]

> Username: Arghnews  
> Created at: 2021-08-11 23:19:19 UTC  
> Updated at: 2021-11-18 19:30:58 UTC  
> Closed at: 2021-11-18 19:30:58 UTC  
> Url: https://github.com/boostorg/container/issues/191  

Compiling the below file on Linux (clang10):  
  
`clang++ -std=c++17 a.cpp -I/my/path/to/boost/include -Wconversion`  
  
```cpp  
// a.cpp  
#include "boost/container/small_vector.hpp"  
int main() {  
  boost::container::small_vector<int, 4> v;  
  (void)(v.end());  
  (void)(v.cend());  
}  
```  
  
Results in warnings from [this](https://github.com/boostorg/container/blob/bfbab6ed7f641f62ba53093a9ae205970039e2b4/include/boost/container/vector.hpp#L1403) and [this](https://github.com/boostorg/container/blob/bfbab6ed7f641f62ba53093a9ae205970039e2b4/include/boost/container/vector.hpp#L1467) line in [boost/container/vector.hpp](https://github.com/boostorg/container/blob/develop/include/boost/container/vector.hpp)  
  
due to the comparison in unsigned and signed types.  
  
These can both be fixed by changing the mentioned lines from  
`it += this->m_holder.m_size;` to `it += static_cast<difference_type>(this->m_holder.m_size);`  
  
I'm consuming boost via the conan package manager, and it's not built as a system package, hence the warnings that would usually be suppressed.  
  
Apologies if I'm missing something obvious as to why this is like this. Cheers.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-11-18 19:30:58 UTC  
> Url: https://github.com/boostorg/container/issues/191#issuecomment-973189299  

Thanks for the report. The commit:  
  
https://github.com/boostorg/container/commit/dad2cb2d02aeccbf2d47eb0e18d73e52b9c51991  
  
should have fixed all -Wconversion warnings.
