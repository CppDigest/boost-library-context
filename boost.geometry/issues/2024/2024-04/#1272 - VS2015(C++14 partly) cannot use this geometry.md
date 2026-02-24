# #1272 - VS2015(C++14 partly) cannot use this geometry [Closed]

> Username: xiaofeiyun123  
> Created at: 2024-04-06 14:41:54 UTC  
> Updated at: 2024-06-27 08:36:19 UTC  
> Closed at: 2024-06-27 08:36:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/1272  

Is there a way to use boost.geometry closest_points in VS2015(C++14 partly)?   
Or other possible library has similar function with boost.geometry closest_points?  
Because I indeed need to develop in the enviroment of VS2015.  
  
Compile error when build with VS2015  
#if defined(BOOST_NO_CXX14_CONSTEXPR)  
#error "Use C++14 or higher to compile Boost.Geometry, or use Boost 1.72 or lower."  
#endif  
  
Compile passed when build with VS2017

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-04-24 13:10:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/1272#issuecomment-2074914098  

C++14 is the minimal requirement.  
If that is not supported - then you have to use Boost 1.72 or lower.
