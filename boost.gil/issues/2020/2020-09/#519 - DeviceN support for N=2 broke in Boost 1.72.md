# #519 - DeviceN support for N=2 broke in Boost 1.72 [Closed]

> Username: dmwilk  
> Created at: 2020-09-09 00:34:12 UTC  
> Updated at: 2022-05-18 07:30:07 UTC  
> Closed at: 2022-05-18 07:30:07 UTC  
> Url: https://github.com/boostorg/gil/issues/519  

Existing code using any of the boost::gil::dev2n32f types (defined in typedefs.hpp) fails because of explicit static assertion check. I would expect if gil is going to continue defining these types that they continue to compile; explicit exclusion of support for channels=2 is surprising given the number of useful formats this represents (2D flow vectors, grayscale images with alpha, duotones)  
  
My system uses DeviceN=2 extensively & refactoring is a nontrivial endeavor  
  
### C++ Minimal Working Example  
  
```#include <boost/gil.hpp>  
int main  
{  
	boost::gil::dev2n32f_pixel_t	this_wont_compile;    // defined in typedefs.hpp  
	boost::gil::dev3n32f_pixel_t	this_does_compile;  
  
	boost::gil::image<boost::gil::dev2n32f_pixel_t, false>		nope;  
	boost::gil::image<boost::gil::dev3n32f_pixel_t, false>		yep;  
        return 0;  
}  
```  
failure is in a static_assert expressing non-implementation so should be same in all compilers

---

## Comment 1

> Username: striezel  
> Created at: 2022-04-29 20:23:42 UTC  
> Updated at: 2022-04-29 20:24:09 UTC  
> Url: https://github.com/boostorg/gil/issues/519#issuecomment-1113693735  

> failure is in a static_assert expressing non-implementation so should be same in all compilers  
  
Possibly yes, but in my opinion it would have been a good idea to include the message here nonetheless, so people might get a better idea where to look when trying to fix this error.  
  
Using the provided minimal working example (MWE)  
```  
#include <boost/gil.hpp>  
  
int main()  
{  
	boost::gil::dev2n32f_pixel_t	this_wont_compile;    // defined in typedefs.hpp  
	boost::gil::dev3n32f_pixel_t	this_does_compile;  
  
	boost::gil::image<boost::gil::dev2n32f_pixel_t, false>		nope;  
	boost::gil::image<boost::gil::dev3n32f_pixel_t, false>		yep;  
        return 0;  
}  
```  
I get the following error in GCC 11.2.0 using the current master from boostorg/boost@38e705a8e39e679e9bf2be7fa28155239d2c7ee0:  
  
```  
$ g++ main.cpp   
In file included from /usr/include/boost/gil/typedefs.hpp:13,  
                 from /usr/include/boost/gil/channel_algorithm.hpp:13,  
                 from /usr/include/boost/gil/color_convert.hpp:11,  
                 from /usr/include/boost/gil/image_view_factory.hpp:11,  
                 from /usr/include/boost/gil/algorithm.hpp:15,  
                 from /usr/include/boost/gil.hpp:12,  
                 from main.cpp:1:  
/usr/include/boost/gil/device_n.hpp: In instantiation of 'struct boost::gil::devicen_t<2>':  
/usr/include/boost/gil/device_n.hpp:56:8:   required from 'struct boost::gil::devicen_layout_t<2>'  
/usr/include/boost/gil/pixel.hpp:99:8:   required from 'struct boost::gil::pixel<boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, boost::gil::devicen_layout_t<2> >'  
main.cpp:5:31:   required from here  
/usr/include/boost/gil/device_n.hpp:46:16: error: static assertion failed: invalid number of DeviceN color components  
   46 |         N == 1 || (3 <= N && N <= 5),  
      |         ~~~~~~~^~~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/gil/device_n.hpp:46:16: note: '((2 == 1) || ((3 <= 2) && (2 <= 5)))' evaluates to false  
```  
  
On the other hand, it compiles with Boost 1.71 just fine. Compiling with 1.72 fails, error message is the same.  
The `static_assert` was introduced in commit 5611bd58071873d6346fe3cc05fe86c72f697717 of PR #274.  
  
I'll try to fix this.

---

## Comment 2

> Username: striezel  
> Created at: 2022-04-29 20:44:11 UTC  
> Updated at: 2022-04-29 20:44:22 UTC  
> Url: https://github.com/boostorg/gil/issues/519#issuecomment-1113711810  

@dmwilk:  
The changes in #654 fix your issue. At least your minimal working example is now indeed a working example again. :)

---

## Comment 3

> Username: mloskot  
> Created at: 2022-05-18 06:50:37 UTC  
> Url: https://github.com/boostorg/gil/issues/519#issuecomment-1129636807  

@dmwilk Thanks for reporting and thank you @striezel for the fix!
