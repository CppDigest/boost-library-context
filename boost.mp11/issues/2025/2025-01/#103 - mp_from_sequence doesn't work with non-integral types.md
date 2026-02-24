# #103 - mp_from_sequence doesn't work with non-integral types [Closed]

> Username: brevzin  
> Created at: 2025-01-07 18:23:10 UTC  
> Updated at: 2025-01-08 01:41:50 UTC  
> Closed at: 2025-01-08 01:41:50 UTC  
> Url: https://github.com/boostorg/mp11/issues/103  

This snippet:  
```cpp  
enum class E { one, two, three };  
using S = std::integer_sequence<E, E::one, E::two, E::three>;  
using L = mp_from_sequence<S>; // error  
```  
Doesn't compile, complaining about:  
```  
boost/mp11/algorithm.hpp:331:41: error: no match for 'operator+' (operand types are 'const int' and 'E')  
  331 |     using type = mp_list_c<U, (F::value + J)...>;  
      |                               ~~~~~~~~~~^~~~  
```  
  
Would be nice if `mp_from_sequence<S>` just worked for any sequence. If I'm explicitly providing an offset, then that's on me.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-01-07 18:55:13 UTC  
> Url: https://github.com/boostorg/mp11/issues/103#issuecomment-2576010557  

Accidentally broken by https://github.com/boostorg/mp11/commit/b7da04c5941968cd21f02e2dd9dd5174a87b4f28.
