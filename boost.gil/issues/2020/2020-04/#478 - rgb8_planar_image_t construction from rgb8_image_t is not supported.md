# #478 - rgb8_planar_image_t construction from rgb8_image_t is not supported [Closed]

> Username: mloskot  
> Created at: 2020-04-08 18:56:51 UTC  
> Updated at: 2021-01-30 20:12:07 UTC  
> Closed at: 2021-01-30 20:11:51 UTC  
> Url: https://github.com/boostorg/gil/issues/478  

This issue was reported by @sdebionne on Slack.  
  
I don't know if this is a bug or an undocumented limitation (see below), but there may be not too complex ways to fix it (e.g. planar image copy-construction from interleaved image by zero-filling construction followed by `copy_pixels` with interleaved to planar pixel conversion; slower than `uninitialized_copy` though). See more details below.  
  
/cc @stefanseefeld @chhenning in case you know more for background of this limitation (or bug).  
  
### Actual behavior  
  
```cpp  
gil::rgb8_image_t            img1(gil::rgb8_planar_image_t{}); // OK  
gil::rgb8_planar_image_t img2(gil::rgb8_image_t{});             // KO (fails to compile)  
```  
  
### Expected  behavior  
  
<!-- A clear and concise description of what you expected to happen. -->  
  
Successful construction of planar images from interleaved images.  
  
### C++ Minimal Working Example  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
```cpp  
#include <boost/gil.hpp>  
namespace gil = boost::gil;  
int main()  
{  
    gil::rgb8_image_t img_i;  
    gil::rgb8_planar_image_t img_p(img_i); // fails to compile  
  
    // due to no overload of uninitialized_copy_aux supporting  
    // input pair of interleaved iterators and output pair of planar iterators  
    using both_planar = std::false_type;  
    auto iv = gil::view(img_i);  
    auto pv = gil::view(img_p);  
  
    // fails to compile  
    gil::detail::uninitialized_copy_aux(iv.begin().x(), iv.end().x(), pv.begin().x(), both_planar{});  
  
    // compiles, becuase this direction is supported  
    gil::detail::uninitialized_copy_aux(pv.begin().x(), pv.end().x(), iv.begin().x(), both_planar{});  
}  
```  
  
### Discussion  
  
Apparently, this comment here is not entirely correct or complete:  
  
https://github.com/boostorg/gil/blob/b3f72cc1d64f8d3e5b5121db5c6c2a1fb4663ff0/include/boost/gil/algorithm.hpp#L764-L767  
  
and should rather read  
  
```cpp  
/// std::uninitialized_copy for pairs of interleaved iterators or input pair of planar iterators and output pair of interleaved iterators  
```  
  
The `gil::detail::uninitialized_copy_aux` simply performs https://en.cppreference.com/w/cpp/memory/uninitialized_copy which requires  
  
```cpp  
::new (static_cast<void*>(std::addressof(*current))) Value(*first);  
```  
  
that is not feasible for planar pixels.  
  
### References  
  
- Revealed during tests of PR #477  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Version (Git ref or `<boost/version.hpp>`): Boost 1.72, 1.73 (current master and develop)
