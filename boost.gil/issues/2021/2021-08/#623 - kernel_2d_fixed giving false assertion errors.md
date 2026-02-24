# #623 - kernel_2d_fixed giving false assertion errors [Open]

> Username: meshtag  
> Created at: 2021-08-04 07:28:44 UTC  
> Updated at: 2021-08-20 13:24:43 UTC  
> Url: https://github.com/boostorg/gil/issues/623  

### Actual behavior  
  
<!-- A clear description of what happens -->  
``` boost::gil::detail::kernel_2d_fixed ``` gives false assertion errors during its initialization.  
  
### Expected  behavior  
A 2D kernel with provided values and center co-ordinates should be created  
  
<!-- A clear and concise description of what you expected to happen. -->  
  
### C++ Minimal Working Example  
https://wandbox.org/permlink/apUAhyheZUyea3UO  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
```cpp  
#include <boost/gil.hpp>  
namespace gil = boost::gil;  
int main  
{  
    // your code  
    std::array<int, 81> v = {0};  
    gil::detail::kernel_2d_fixed<int, 9> kernel(v.cbegin(), 4, 4); // Not ok  
}  
```  
This problem remained unnoticed in CI build due to [this](https://github.com/boostorg/gil/blob/7dd61209a81695600d773f481bb8b22b0f74a30a/test/extension/numeric/kernel_fixed.cpp#L9) macro.  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version: gcc 11.1.0  
- Version: Boost 1.76.0

---

## Comment 1

> Username: meshtag  
> Created at: 2021-08-04 07:33:20 UTC  
> Updated at: 2021-08-20 13:14:38 UTC  
> Url: https://github.com/boostorg/gil/issues/623#issuecomment-892434587  

A simple workaround for this problem is [here.](https://github.com/meshtag/gil/blob/6b071c8c959c77dfa6c581491b266aef3aef32f7/include/boost/gil/extension/numeric/kernel.hpp#L246) However, I think this issue can be dealt with in a more reliable/generic manner.
