# #108 - Some algorithms don't work with non-variadic lists [Closed]

> Username: joaquintides  
> Created at: 2025-10-21 08:42:14 UTC  
> Updated at: 2025-10-21 22:58:03 UTC  
> Closed at: 2025-10-21 22:58:03 UTC  
> Url: https://github.com/boostorg/mp11/issues/108  

For example:  
```cpp  
#include <boost/mp11.hpp>  
#include <type_traits>  
#include <utility>  
  
template<typename T1, typename T2> struct F;  
  
int main()  
{  
  using namespace boost::mp11;  
  
  using l1 =  mp_reverse_fold<mp_list<int, char>, void, F>;  
  static_assert(std::is_same_v<l1, F<int, F<char, void>>>);  
  
  using l2 =  mp_reverse_fold<std::pair<int, char>, void, F>; // fails  
}  
```  
(https://godbolt.org/z/d8PW59Mse)  
  
An obvious solution would be for `mp_reverse_fold` to internally use `mp_rename<L, mp_list>` instead of `L`. I suspect other algorithms may be affected as well.
