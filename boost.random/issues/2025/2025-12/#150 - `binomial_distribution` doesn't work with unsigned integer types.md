# #150 - `binomial_distribution` doesn't work with unsigned integer types [Closed]

> Username: KRM7  
> Created at: 2025-12-01 16:30:40 UTC  
> Updated at: 2025-12-02 13:54:17 UTC  
> Closed at: 2025-12-02 13:54:17 UTC  
> Url: https://github.com/boostorg/random/issues/150  

Code using `boost::binomial_distribution` fails to compile if the template argument is an unsigned integer type, due to an ambiguous call to `abs` in the implementation of `generate()`.   
https://godbolt.org/z/MKKGz3b4r  
```cpp  
#include <boost/random.hpp>  
#include <random>  
#include <cstddef>  
  
std::mt19937_64 prng(0);  
boost::random::binomial_distribution<uint64_t> dist;  
  
uint64_t test(uint64_t num) {  
    return dist(prng);  
}  
```  
  
The ambiguous call is here:  
https://github.com/boostorg/random/blob/be37499139d93d5f928bd5a97562346a92100847/include/boost/random/binomial_distribution.hpp#L324  
  
Is this the intended behaviour? I haven't seen anything in the docs that would mention that the template argument must be signed, and `std::binomial_distribution` supports unsigned integer types.

---

## Comment 1

> Username: mborland  
> Created at: 2025-12-02 08:37:04 UTC  
> Url: https://github.com/boostorg/random/issues/150#issuecomment-3600851807  

No, this should work with unsigned integer types much like `std::`. I will look into it.
