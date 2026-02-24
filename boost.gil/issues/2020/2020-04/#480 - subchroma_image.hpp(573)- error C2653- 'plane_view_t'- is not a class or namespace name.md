# #480 - subchroma_image.hpp(573): error C2653: 'plane_view_t': is not a class or namespace name [Closed]

> Username: mloskot  
> Created at: 2020-04-10 20:05:03 UTC  
> Updated at: 2020-04-11 11:23:33 UTC  
> Closed at: 2020-04-11 11:23:33 UTC  
> Url: https://github.com/boostorg/gil/issues/480  

### Actual behavior  
  
<!-- A clear description of what happens -->  
  
`boost/gil/extension/toolbox/image_types/subchroma_image.hpp` is not self-contained or is broken as it does not compile.  
  
### Expected  behavior  
  
<!-- A clear and concise description of what you expected to happen. -->  
  
Successful compilation of the header.  
  
### C++ Minimal Working Example  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
```cpp  
#include <boost/gil/extension/toolbox/image_types/subchroma_image.hpp>  
int main()  
{  
}  
```  
  
The reported error location in line `573` is non-sense  
  
https://github.com/boostorg/gil/blob/5611bd58071873d6346fe3cc05fe86c72f697717/include/boost/gil/extension/toolbox/image_types/subchroma_image.hpp#L573  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version:  MSVC 14.1 and 14.2  
- Build settings: `-std:c++latest` and `-permissive-`  
- Version (Git ref or `<boost/version.hpp>`): 1.72 and current develop
