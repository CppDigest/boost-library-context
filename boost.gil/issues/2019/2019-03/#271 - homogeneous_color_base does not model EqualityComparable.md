# #271 - homogeneous_color_base does not model EqualityComparable [Open]

> Username: mloskot  
> Created at: 2019-03-25 20:19:06 UTC  
> Updated at: 2019-11-07 10:00:58 UTC  
> Url: https://github.com/boostorg/gil/issues/271  

### Minimal Working Example (in C++)  
  
```cpp  
#define BOOST_GIL_USE_CONCEPT_CHECK  
#include <boost/gil/concepts.hpp>  
#include <boost/gil/color_base.hpp>  
#include <boost/concept_check.hpp>  
namespace gil = boost::gil;  
  
int main()  
{  
    function_requires  
    <  
        gil::ColorBaseConcept  
        <  
            gil::detail::homogeneous_color_base<std::uint8_t, gil::rgb_layout_t, 3>  
        >  
    >();  
}  
```  
  
Confront the concept `ColorBaseConcept`  
  
https://github.com/boostorg/gil/blob/6763a8cd983ae9304a77fb6bb9543996be0e4443/include/boost/gil/concepts/color_base.hpp#L134-L140  
  
with definition of `homogeneous_color_base` (for 2, 3 or 4) elements which is supposed to be a model of the concept:  
  
https://github.com/boostorg/gil/blob/54f1817e991c44e42c50fcbf91ad95a37f965a19/include/boost/gil/color_base.hpp#L136-L188  
  
### Actual behavior  
  
The MWE above does not compile due to `homogeneous_color_base` lacking of:  
  
```  
binary '==': no operator found   
binary '!=': no operator found   
```  
  
### Expected  behavior  
  
The `homogeneous_color_base` should be a complete model of `ColorBaseConcept` just like `gil::rgb8_pixel_t` and other specializations of the `pixel` class, which does define equality operators.  
  
-----  
  
It looks like this single-element specialization is `EqualComparable` by accident thanks to the non-`explicit` operator converting the type to integral type of channel value:  
  
https://github.com/boostorg/gil/blob/54f1817e991c44e42c50fcbf91ad95a37f965a19/include/boost/gil/color_base.hpp#L69-L82  
  
----  
  
/cc @chhenning , @stefanseefeld Any thoughts on this issue is much appreciated.
