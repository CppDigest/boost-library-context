# #615 - Add arithmetic operators for float and double grayscale pixels [Open]

> Username: sdebionne  
> Created at: 2021-06-10 08:06:50 UTC  
> Updated at: 2021-06-10 08:06:50 UTC  
> Url: https://github.com/boostorg/gil/issues/615  

### Is your feature request related to a problem? Please describe.  
  
I was surprised to see that arithmetic operations on `pixel` compiles but without explicit support from the library. `pixel`s are actually implicitly converted to their underlying type.  
  
### Describe the solution you'd like  
  
Provide a set of arithmetic operators, at least for `pixel_gray32f_t` and `pixel_gray64f_t`.  
  
#### C++ Example  
  
<!-- C++ program or snippet that shows preferred solution -->  
  
```cpp  
#include <type_traits>  
#include <boost/gil/pixel.hpp>  
using pixel_t = boost::gil::pixel<float, boost::gil::layout<boost::mp11::mp_list<boost::gil::gray_color_t> > >;  
int main()  
{  
    pixel_t a(1.0f), b(2.0f);  
    auto c = a + b;  
    static_assert(std::is_same_v<std::decay_t<decltype(c)>, float>);   //OK , but is implicit conversion right?  
    static_assert(std::is_same_v<std::decay_t<decltype(c)>, pixel_t>); //Fails, but should be OK?  
    return 0;  
}  
```  
  
### Additional context  
  
Discussed first on [Slack](https://cpplang.slack.com/archives/CSVT0STV2/p1622797174115100)
