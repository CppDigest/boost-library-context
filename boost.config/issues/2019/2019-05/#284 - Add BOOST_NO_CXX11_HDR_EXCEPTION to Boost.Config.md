# #284 - Add BOOST_NO_CXX11_HDR_EXCEPTION to Boost.Config [Closed]

> Username: dmenendez-gruposantander  
> Created at: 2019-05-30 13:09:07 UTC  
> Updated at: 2019-08-26 08:32:47 UTC  
> Closed at: 2019-08-26 08:32:47 UTC  
> Url: https://github.com/boostorg/config/issues/284  

BOOST_NO_CXX11_HDR_EXCEPTION => The standard library does not provide a C++11 compatible version of <exception>.  
  
In particular, for Issue boostorg/exception#10 I would need to detect compiler support for C++11 std::exception_ptr and std::current_exception().  
  
I'll try to come up with a PR, but I can only test on two compilers (VS2015 and VS2017) and both provide a C++11 compatible <exception>.  
  
Is there any way I can test a PR against remote testers using other compilers?
