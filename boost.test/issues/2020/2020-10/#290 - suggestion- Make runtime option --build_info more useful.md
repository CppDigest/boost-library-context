# #290 - suggestion: Make runtime option  --build_info more useful [Open]

> Username: correaa  
> Created at: 2020-10-31 09:27:41 UTC  
> Updated at: 2020-10-31 09:27:58 UTC  
> Url: https://github.com/boostorg/test/issues/290  

The option [--build_info[=boolean value]](https://www.boost.org/doc/libs/1_74_0/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/build_info.html)  is quite useless now, at least in linux because it report the compiler and environment variables with which Boost.Test binaries were compiled. It does not report the compiler with which the actual test are compiled.  
  
**Can this behavior be changed in a future version of the library?**  
  
I have the impression that if the implementation of the class `boost::unit_test::output::compiler_log_formatter` were to be moved to an hpp file that limitation would be solved.
