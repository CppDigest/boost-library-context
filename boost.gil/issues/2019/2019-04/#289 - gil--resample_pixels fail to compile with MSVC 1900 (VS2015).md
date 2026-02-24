# #289 - [Boost 1.70] gil::resample_pixels fail to compile with MSVC 1900 (VS2015) [Closed]

> Username: Cry-Thomas  
> Created at: 2019-04-16 14:08:19 UTC  
> Updated at: 2019-05-02 12:39:55 UTC  
> Closed at: 2019-04-29 18:59:52 UTC  
> Url: https://github.com/boostorg/gil/issues/289  

### Minimal Working Example (in C++)  
  
```cpp  
#include <boost/gil/extension/numeric/resample.hpp>  
#include <boost/gil/extension/numeric/sampler.hpp>  
  
int main()  
{  
	boost::gil::bgra8_image_t image1;  
	boost::gil::bgra8_image_t image2;  
  
	boost::gil::matrix3x2<double> mat =  
		boost::gil::matrix3x2<double>::get_translate(-boost::gil::point2<double>(0.5, 0.5)) *  
		boost::gil::matrix3x2<double>::get_rotate(0.5);  
  
	boost::gil::resample_pixels(boost::gil::const_view(image1), boost::gil::view(image2), mat, boost::gil::bilinear_sampler());  
  
	return 0;  
}  
```  
  
### Actual behavior  
compilation fails with following error message:  
```  
1>c:\program files (x86)\microsoft visual studio 14.0\vc\include\type_traits(1174): error C2446: ':': no conversion from 'boost::gil::matrix3x2<double>' to 'std::streamsize'  
1>         c:\program files (x86)\microsoft visual studio 14.0\vc\include\type_traits(1174): note: No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>         c:\users\thomasw\desktop\boost_gil_error_test\boost\boost\gil\extension\numeric\affine.hpp(89): note: see reference to class template instantiation 'std::common_type<ptrdiff_t,boost::gil::matrix3x2<double>>' being compiled  
1>         c:\users\thomasw\desktop\boost_gil_error_test\boost\boost\gil\extension\numeric\resample.hpp(46): note: see reference to function template instantiation 'boost::gil::point<double> boost::gil::transform<double,ptrdiff_t>(const boost::gil::matrix3x2<double> &,const boost::gil::point<ptrdiff_t> &)' being compiled  
1>         c:\users\thomasw\desktop\boost_gil_error_test\boost_gil_error_test\boost_gil_error_test.cpp(15): note: see reference to function template instantiation 'void boost::gil::resample_pixels<boost::gil::bilinear_sampler,boost::gil::image_view<boost::gil::bgra8c_loc_t>,boost::gil::image_view<boost::gil::bgra8_loc_t>,boost::gil::matrix3x2<double>>(const SrcView &,const DstView &,const MapFn &,Sampler)' being compiled  
1>                 with  
1>                 [  
1>                     SrcView=boost::gil::image_view<boost::gil::bgra8c_loc_t>,  
1>                     DstView=boost::gil::image_view<boost::gil::bgra8_loc_t>,  
1>                     MapFn=boost::gil::matrix3x2<double>,  
1>                     Sampler=boost::gil::bilinear_sampler  
1>                 ]  
1>     1>  
1>c:\program files (x86)\microsoft visual studio 14.0\vc\include\type_traits(1175): error C2955: 'std::decay': use of class template requires template argument list  
1>         c:\program files (x86)\microsoft visual studio 14.0\vc\include\type_traits(990): note: see declaration of 'std::decay'  
1>  
1>Build FAILED.  
```  
### Expected  behavior  
compilation ends successfully  
  
### Environment  
IDE: Visual Studio 2015  
Toolchain: vc140  
MSVC version: 1900  
boost version: 1.70

---

## Comment 1

> Username: mloskot  
> Created at: 2019-04-29 19:06:41 UTC  
> Url: https://github.com/boostorg/gil/issues/289#issuecomment-487704785  

@Cry-Thomas Thanks for reporting this issue.  
  
It's should be fixed now by PR #295

---

## Comment 2

> Username: Cry-Thomas  
> Created at: 2019-04-29 19:44:15 UTC  
> Url: https://github.com/boostorg/gil/issues/289#issuecomment-487716797  

Thanks a bunch @mloskot! We appreciate the swift solution and heads up of coming changes!

---

## Comment 3

> Username: mloskot  
> Created at: 2019-05-02 12:39:55 UTC  
> Url: https://github.com/boostorg/gil/issues/289#issuecomment-488656532  

Addition of SFINAE-friendly version of `std::common_type` in #298 was also required to complete the fix.
