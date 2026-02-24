# #629 - Cannot compile any_image example from the docs [Closed]

> Username: HappySeaFox  
> Created at: 2021-11-06 18:17:18 UTC  
> Updated at: 2021-11-08 09:20:43 UTC  
> Closed at: 2021-11-08 09:20:43 UTC  
> Url: https://github.com/boostorg/gil/issues/629  

Hi! Could you please help with compiling the code sample from the GIL docs:  
  
### Actual behavior  
  
I copy-pasted the following code sample from the docs   
  
```  
#include <boost/gil.hpp>  
#include <boost/gil/extension/dynamic_image/any_image.hpp>  
#include <boost/gil/extension/io/jpeg.hpp>  
#include <boost/gil/extension/io/png.hpp>  
#include <boost/mpl/vector.hpp>  
  
using namespace boost;  
using namespace boost::gil;  
  
...  
  
typedef mpl::vector< gray8_image_t  
                   , gray16_image_t  
                   , rgb8_image_t  
                   , rgba8_image_t  
                   > my_img_types;  
  
any_image< my_img_types > runtime_image;  
  
read_image( ""  
          , runtime_image  
          , tiff_tag()  
          );  
```  
  
from https://www.boost.org/doc/libs/1_75_0/libs/gil/doc/html/io.html  
  
Even after fixing `rgba_image_t` -> `rgba8_image_t`, it still complains:  
  
```  
D:\projects\git.vcpkg\installed\x64-windows\include\boost/gil/extension/dynamic_image/any_image.hpp(31,32): error C2794: 'view_t': is not a member of any direct or indirect base class of 'boost::mpl::vector<boost::gil::gray8_image_t,boost::gil::gray16_image_t,boost::gil::rgb8_image_t,boost::gil::rgba8_image_t,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na>' [D:\projects\git.sail-benchmark\build\sail-benchmark-gil.vcxproj]  
D:\projects\git.vcpkg\installed\x64-windows\include\boost/mp11/algorithm.hpp(52): message : see reference to alias template instantiation 'boost::gil::detail::get_view_t<my_img_types>' being compiled [D:\projects\git.sail-benchmark\build\sail-benchmark-gil.vcxproj]  
D:\projects\git.vcpkg\installed\x64-windows\include\boost/gil/extension/dynamic_image/any_image.hpp(34): message : see reference to class template instantiation 'boost::mp11::detail::mp_transform_impl<boost::gil::detail::get_view_t,boost::gil::any_image<my_img_types>>' being compiled [D:\projects\git.sail-benchmark\build\sail-benchmark-gil.vcxproj]  
D:\projects\git.vcpkg\installed\x64-windows\include\boost/gil/extension/dynamic_image/any_image.hpp(34): message : see reference to alias template instantiation 'boost::mp11::mp_transform<boost::gil::detail::get_view_t,boost::gil::any_image<my_img_types>>' being compiled [D:\projects\git.sail-benchmark\build\sail-benchmark-gil.vcxproj]  
D:\projects\git.vcpkg\installed\x64-windows\include\boost/gil/extension/dynamic_image/any_image.hpp(93): message : see reference to alias template instantiation 'boost::gil::detail::images_get_views_t<boost::gil::any_image<my_img_types>>' being compiled [D:\projects\git.sail-benchmark\build\sail-benchmark-gil.vcxproj]  
D:\projects\git.sail-benchmark\src\sail-benchmark-gil.cpp(66): message : see reference to class template instantiation 'boost::gil::any_image<my_img_types>' being compiled [D:\projects\git.sail-benchmark\build\sail-benchmark-gil.vcxproj]  
```  
  
### Expected  behavior  
  
It compiles.  
  
### C++ Minimal Working Example  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version: MSVC 2019  
- Build settings:  
- Version (Git ref or `<boost/version.hpp>`): 1.77 in `vcpkg`

---

## Comment 1

> Username: sdebionne  
> Created at: 2021-11-08 09:07:35 UTC  
> Url: https://github.com/boostorg/gil/issues/629#issuecomment-962946731  

Indeed, the documentation is not up-to-date. The need for an MPL vector has been removed, the `any_image` variant should be instantiated directly with:  
  
```  
any_image< gray8_image_t  
         , gray16_image_t  
         , rgb8_image_t  
         , rgba8_image_t > runtime_image;  
```

---

## Comment 2

> Username: HappySeaFox  
> Created at: 2021-11-08 09:20:42 UTC  
> Url: https://github.com/boostorg/gil/issues/629#issuecomment-962958489  

Cool, thanks! 😀
