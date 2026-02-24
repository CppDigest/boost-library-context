# #78 - static_vector: Can't emplace a non-movable non-copyable type. [Closed]

> Username: jdf98  
> Created at: 2018-08-14 13:33:24 UTC  
> Updated at: 2018-11-10 23:38:24 UTC  
> Closed at: 2018-11-10 23:38:24 UTC  
> Url: https://github.com/boostorg/container/issues/78  

The following code fails to compile, because it tries to move `X` instead of constructing it in-place. Note that using `emplace_back` is fine.  
Tested on godbolt.org with no additional compiler options on:  
- boost 1.64.0, 1.68.0 / x86-64 gcc 7.x, 8.x  
- boost 1.64.0, 1.68.0 / x86-64 clang 4.0.x, 5.0.0, 6.0.0  
- boost 1.64.0, 1.68.0 / x86-64 MSVC 19 2017 RTW  
  
```cpp  
#include <boost/container/static_vector.hpp>  
  
struct X {  
  X() = delete;  
  X(X const&) = delete;  
  X(X&&) = delete;  
  X& operator=(X const&) = delete;  
  X& operator=(X&&) = delete;  
  
  int a;  
  bool b;  
  X(int a, bool b) {  
  }  
};  
  
int main() {  
    boost::container::static_vector<X, 1> v;  
    v.emplace(v.cend(), 4, true);  
    // v.emplace_back(4, true); // ok  
    return 0;  
}  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-11-10 23:38:24 UTC  
> Url: https://github.com/boostorg/container/issues/78#issuecomment-437630532  

The implementation of "emplace", in the general case, needs to move elements (if a value is emplaced in the middle). Passing an end() iterator is a runtime feature, so it's impossible to know at compile time that no element needs to be moved (whereas in "emplace_back", code knows at compile time no element needs to be moved around). I'm closing this as an invalid bug report.
