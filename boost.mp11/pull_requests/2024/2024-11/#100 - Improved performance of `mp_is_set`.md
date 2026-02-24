# #100 Improved performance of  `mp_is_set` [Merged]

> Username: joaquintides  
> Created at: 2024-11-08 19:43:02 UTC  
> Updated at: 2024-11-09 01:46:10 UTC  
> Merged at: 2024-11-09 01:46:10 UTC  
> Closed at: 2024-11-09 01:46:10 UTC  
> Url: https://github.com/boostorg/mp11/pull/100  

Crude compile times on my local machine for this testing code:  
  
```cpp  
#include <boost/mp11.hpp>  
using namespace boost::mp11;  
  
constexpr int N = 16;  
  
using L1 = mp_iota_c<N>;  
using L2 = mp_iota_c<N*2>;  
  
template<class T> using Pr = mp_is_set<mp_push_back<L1, T>>;  
  
using R = mp_count_if<L2, Pr>;  
  
static_assert( R::value == N, "" );  
  
int main()  
{  
}  
```  
![imagen](https://github.com/user-attachments/assets/bf5d20fa-b577-49db-a71f-3a80d1962efc)

---
