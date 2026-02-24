# #205 - Boost failed with error C2440 when build with permissive- on MSVC [Open]

> Username: QuellaZhang  
> Created at: 2018-09-18 05:47:45 UTC  
> Updated at: 2018-09-18 05:47:45 UTC  
> Url: https://github.com/boostorg/hof/issues/205  

Hi All,  
  
We tried to build and run hof test for Boost with permissive- mode on VS2017 Update 7 on Windows. It failed to build due to the error C2440. Could you please help take a look at this? Thank you!  
  
**Reproduce steps:**  
  
1. git clone -c core.autocrlf=true --recursive https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2015 x86 command prompt and browse to D:\Boost\src  
3. set CL=/DNOMINMAX /wd4643 /permissive-  
4. .\bootstrap  
5. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=32  
6. .\b2 variant=release --build-dir=..\out\x86rel address-model=32  
7. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\hof\test  
  
**Failures:**  
 first_of.cpp  
 libs\hof\test\first_of.cpp(159): error C2440: 'initializing': cannot convert from 'boost::hof::reveal_adaptor<boost::hof::first_of_adaptor<boost::hof::detail::static_function_wrapper,boost::hof::detail::static_function_wrapper<conditional_test::<lambda_231d8bd3f8f18b9501196a787ccc297d>>,boost::hof::detail::static_function_wrapper<conditional_test::<lambda_48604b67640df949e9400536c4c96d52>>>,boost::hof::first_of_adaptor<boost::hof::detail::static_function_wrapper,boost::hof::detail::static_function_wrapper<conditional_test::<lambda_231d8bd3f8f18b9501196a787ccc297d>>,boost::hof::detail::static_function_wrapper<conditional_test::<lambda_48604b67640df949e9400536c4c96d52>>>>' to 'boost::hof::reveal_adaptor<boost::hof::first_of_adaptor<boost::hof::detail::static_function_wrapper,boost::hof::detail::static_function_wrapper<conditional_test::<lambda_231d8bd3f8f18b9501196a787ccc297d>>,boost::hof::detail::static_function_wrapper<conditional_test::<lambda_48604b67640df949e9400536c4c96d52>>>,boost::hof::first_of_adaptor<boost::hof::detail::static_function_wrapper,boost::hof::detail::static_function_wrapper<conditional_test::<lambda_231d8bd3f8f18b9501196a787ccc297d>>,boost::hof::detail::static_function_wrapper<conditional_test::<lambda_48604b67640df949e9400536c4c96d52>>>> &'  
 with  
 [  
 T=conditional_test::<lambda_1e9a0704b43f847d23521fae74403506>  
 ]  
 libs\hof\test\first_of.cpp(159): note: A non-const reference may only be bound to an lvalue
