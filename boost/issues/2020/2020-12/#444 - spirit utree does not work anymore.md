# #444 - spirit utree does not work anymore [Open]

> Username: bpinaud  
> Created at: 2020-12-07 13:00:44 UTC  
> Updated at: 2021-01-27 09:31:35 UTC  
> Url: https://github.com/boostorg/boost/issues/444  

I have a huge parser made with spirit and utree which is working well for quite a long time. Since boost 1.72 (I think), it does not compile anymore (not better with 1.74).  I did not change my code. Just a Boost update from my Linux distro (gentoo). Here is the cryptic message. I hope you can be of any help.  
Here is the error message with Boost-1.74:   
  
 /usr/lib/gcc/x86_64-pc-linux-gnu/9.3.0/../../../../x86_64-pc-linux-gnu/bin/ld: CMakeFiles/strategy-spirit-5.5.0.dir/runstrategyelementvisitors.cpp.o: in function `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)':  
/usr/include/boost/spirit/home/support/utree/operators.hpp:545: multiple definition of `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)'; CMakeFiles/strategy-spirit-5.5.0.dir/RunStrategyElement.cpp.o:/usr/include/boost/spirit/home/support/utree/operators.hpp:545: first defined here  
/usr/lib/gcc/x86_64-pc-linux-gnu/9.3.0/../../../../x86_64-pc-linux-gnu/bin/ld: CMakeFiles/strategy-spirit-5.5.0.dir/Strategy.cpp.o: in function `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)':  
/usr/include/boost/spirit/home/support/utree/operators.hpp:545: multiple definition of `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)'; CMakeFiles/strategy-spirit-5.5.0.dir/RunStrategyElement.cpp.o:/usr/include/boost/spirit/home/support/utree/operators.hpp:545: first defined here  
/usr/lib/gcc/x86_64-pc-linux-gnu/9.3.0/../../../../x86_64-pc-linux-gnu/bin/ld: CMakeFiles/strategy-spirit-5.5.0.dir/StrategyElement.cpp.o: in function `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)':  
/usr/include/boost/spirit/home/support/utree/operators.hpp:545: multiple definition of `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)'; CMakeFiles/strategy-spirit-5.5.0.dir/RunStrategyElement.cpp.o:/usr/include/boost/spirit/home/support/utree/operators.hpp:545: first defined here  
/usr/lib/gcc/x86_64-pc-linux-gnu/9.3.0/../../../../x86_64-pc-linux-gnu/bin/ld: CMakeFiles/strategy-spirit-5.5.0.dir/UtreeWalk.cpp.o: in function `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)':  
/usr/include/boost/spirit/home/support/utree/operators.hpp:545: multiple definition of `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)'; CMakeFiles/strategy-spirit-5.5.0.dir/RunStrategyElement.cpp.o:/usr/include/boost/spirit/home/support/utree/operators.hpp:545: first defined here  
collect2: error: ld returned 1 exit status  
  
Best,  
Bruno

---

## Comment 1

> Username: pmalic  
> Created at: 2021-01-27 09:31:35 UTC  
> Url: https://github.com/boostorg/boost/issues/444#issuecomment-768156980  

This is a bug introduced in Spirit utree in Boost 1.74, is now fixed, see https://github.com/boostorg/spirit/issues/638
