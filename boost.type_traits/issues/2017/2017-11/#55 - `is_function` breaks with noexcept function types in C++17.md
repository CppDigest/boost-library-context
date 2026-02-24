# #55 - `is_function` breaks with noexcept function types in C++17 [Closed]

> Username: gnaggnoyil  
> Created at: 2017-11-05 20:48:24 UTC  
> Updated at: 2018-02-02 04:59:16 UTC  
> Closed at: 2018-02-01 19:15:12 UTC  
> Url: https://github.com/boostorg/type_traits/issues/55  

`boost::is_function` currently does not handle noexcept function types which is distinct from non-noexcept function types. e.g.  
  
```cpp  
#include <boost/type_traits.hpp>  
#include <type_traits>  
  
void foo(int){}  
void bar(int) noexcept{}  
  
int main(){  
    static_assert(boost::is_function<decltype(foo)>::value);  
    static_assert(boost::is_function<decltype(bar)>::value);  
    return 0;  
}  
```  
  
Both GCC 7.2 and Clang 5.0 failed the second assertion if compiled with `-std=c++1z` flag.  
  
Plus, `boost::is_member_function_pointer` has the same issue too.  
  
Boost version is 1.65.1.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-02-01 19:15:12 UTC  
> Url: https://github.com/boostorg/type_traits/issues/55#issuecomment-362371468  

Fixed in https://github.com/boostorg/type_traits/commit/c8c95f18cae16d2847be23834bc1fa9363a3cad8, thanks for the report.

---

## Comment 2

> Username: Flast  
> Created at: 2018-02-02 04:59:16 UTC  
> Url: https://github.com/boostorg/type_traits/issues/55#issuecomment-362486879  

Clang 4 fails deducting partial specialization (clang 5 works fine), and CI fails... It may need split such specializations into two.  
https://wandbox.org/permlink/tKtdJuctETptIaIf
