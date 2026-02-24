# #558 - Suspicious warnings about unused variables [Closed]

> Username: mloskot  
> Created at: 2021-02-02 23:13:39 UTC  
> Updated at: 2022-04-12 16:42:04 UTC  
> Closed at: 2022-04-12 16:42:04 UTC  
> Url: https://github.com/boostorg/gil/issues/558  

These seem to be just  some left-overs, but some may actually be unused by mistake. I'd appreciate if you could have a look @simmplecoder   
  
```  
test/core/rasterization/circle.cpp:22:20: warning: unused variable ‘r_squared’ [-Wunused-variable]  
   22 |     std::ptrdiff_t r_squared = radius * radius;  
      |                    ^~~~~~~~~  
```  
  
```  
../../boost/gil/image_processing/hough_transform.hpp:90:16: warning: unused variable ‘width’ [-Wunused-variable]  
   90 |     const auto width = input.width();  
      |                ^~~~~  
../../boost/gil/image_processing/hough_transform.hpp:91:16: warning: unused variable ‘height’ [-Wunused-variable]  
   91 |     const auto height = input.height();  
      |                ^~~~~~  
```  
  
```  
../../boost/gil/image_processing/diffusion.hpp:155:14: warning: unused variable ‘first’ [-Wunused-variable]  
  155 |         auto first = stencil.begin();  
      |              ^~~~~  
../../boost/gil/image_processing/diffusion.hpp:156:14: warning: unused variable ‘last’ [-Wunused-variable]  
  156 |         auto last = stencil.end();  
      |              ^~~~  
```  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version: GCC 9  
- Build settings: `b2 toolset=gcc cxxstd=11`  
- Version (Git ref or `<boost/version.hpp>`): `develop`

---

## Comment 1

> Username: striezel  
> Created at: 2022-04-12 16:40:32 UTC  
> Url: https://github.com/boostorg/gil/issues/558#issuecomment-1096954475  

@mloskot:  
Those warnings already have been removed in the following commits:  
* commit 2db5fe9f448bbf4b9da0e38414a366ec5188c817 for circle.cpp  
* commit 0b24f4cdbf430430b5430507822b0698cd9d2ac7 for hough_transform.hpp, and  
* commit 5dcdf53c02f6b646703b03670c66596beae905ab for diffusion.hpp (see #635)  
  
In other words: I believe this issue is resolved and can be closed.

---

## Comment 2

> Username: mloskot  
> Created at: 2022-04-12 16:42:04 UTC  
> Url: https://github.com/boostorg/gil/issues/558#issuecomment-1096955999  

Nice, thanks!
