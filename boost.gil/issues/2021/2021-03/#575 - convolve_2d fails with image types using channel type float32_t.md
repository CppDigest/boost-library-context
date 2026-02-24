# #575 - convolve_2d fails with image types using channel type float32_t [Closed]

> Username: harshitpant1  
> Created at: 2021-03-09 18:23:16 UTC  
> Updated at: 2022-02-09 13:43:49 UTC  
> Closed at: 2022-02-09 13:43:49 UTC  
> Url: https://github.com/boostorg/gil/issues/575  

### Actual behavior  
  
convolve_2d fails to convolve images of type with channel type float32_t.  
  
### Expected  behavior  
  
It should work, as it does for other image types.  
  
### C++ Minimal Working Example  
  
```cpp  
#include <boost/gil.hpp>  
#include <vector>  
namespace gil = boost::gil;  
int main()  
{  
    gil::gray32f_image_t img_in(25, 25);  
    gil::gray32f_image_t img_out(img_in.dimensions());  
    std::vector<float> vec{0,0,0,0,1,0,0,0,0};  
    gil::detail::kernel_2d<float> kernel(vec.begin(), vec.size(), 1, 1);  
    gil::detail::convolve_2d(view(img_in), kernel, view(img_out));  
    return 0;   
}  
```  
  
### Environment  
  
- Compiler version: MS Visual C++ version 19.28.29333  
- Version (Git ref or `<boost/version.hpp>`): 107500

---

## Comment 1

> Username: harshitpant1  
> Created at: 2021-03-09 18:26:50 UTC  
> Updated at: 2021-03-09 18:43:01 UTC  
> Url: https://github.com/boostorg/gil/issues/575#issuecomment-794271267  

adding [0], on line 376, seems to work. Perhaps the compiler doesn't like doing two implicit conversions.  
  
```  
aux_total +=  
      src_view(col_boundary, row_boundary)[0] *  
      kernel.at(flip_ker_row, flip_ker_col);   
```
