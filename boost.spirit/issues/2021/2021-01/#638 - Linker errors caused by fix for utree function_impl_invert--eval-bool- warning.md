# #638 - Linker errors caused by fix for utree function_impl_invert::eval<bool> warning [Closed]

> Username: pmalic  
> Created at: 2021-01-21 14:11:30 UTC  
> Updated at: 2021-01-26 20:16:27 UTC  
> Closed at: 2021-01-26 20:16:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/638  

In commit 36f8e09cfd112840085472627a6f2b9e11ff5739 by @Kojoley a warning suppressing pragma was replaced by template specialization for function_impl_invert::eval<T>. This change first landed in Boost 1.74 and is causing linker errors in VC++ 2017 (was previously using 1.73):  
  
```  
1>***.obj : error LNK2005: "public: static class boost::spirit::utree __cdecl boost::spirit::function_impl_invert::eval<bool>(bool const &)" (??$eval@_N@function_impl_invert@spirit@boost@@SA?AVutree@12@AEB_N@Z) already defined in ***.obj  
1>***.obj : error LNK2005: "public: static class boost::spirit::utree __cdecl boost::spirit::function_impl_invert::eval<bool>(bool const &)" (??$eval@_N@function_impl_invert@spirit@boost@@SA?AVutree@12@AEB_N@Z) already defined in ***.obj  
1>***.obj : error LNK2005: "public: static class boost::spirit::utree __cdecl boost::spirit::function_impl_invert::eval<bool>(bool const &)" (??$eval@_N@function_impl_invert@spirit@boost@@SA?AVutree@12@AEB_N@Z) already defined in ***.obj  
1>***.obj : error LNK2005: "public: static class boost::spirit::utree __cdecl boost::spirit::function_impl_invert::eval<bool>(bool const &)" (??$eval@_N@function_impl_invert@spirit@boost@@SA?AVutree@12@AEB_N@Z) already defined in ***.obj  
```  
  
No errors if I just revert the changes done in the mentioned commit.

---

## Comment 1

> Username: pmalic  
> Created at: 2021-01-26 14:53:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/638#issuecomment-767596505  

Linker errors also happening on Linux with GCC 9.3.1:  
  
```  
/opt/rh/devtoolset-9/root/usr/libexec/gcc/x86_64-redhat-linux/9/ld: ***.o: in function `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)':  
/***/boost/include/boost/spirit/home/support/utree/operators.hpp:545: multiple definition of `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)'; ***.o:/***/boost/include/boost/spirit/home/support/utree/operators.hpp:545: first defined here  
/opt/rh/devtoolset-9/root/usr/libexec/gcc/x86_64-redhat-linux/9/ld: ***.o: in function `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)':  
/***/boost/include/boost/spirit/home/support/utree/operators.hpp:545: multiple definition of `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)'; ***.o:/***/boost/include/boost/spirit/home/support/utree/operators.hpp:545: first defined here  
/opt/rh/devtoolset-9/root/usr/libexec/gcc/x86_64-redhat-linux/9/ld: ***.o: in function `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)':  
/***/boost/include/boost/spirit/home/support/utree/operators.hpp:545: multiple definition of `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)'; ***.o:/***/boost/include/boost/spirit/home/support/utree/operators.hpp:545: first defined here  
/opt/rh/devtoolset-9/root/usr/libexec/gcc/x86_64-redhat-linux/9/ld: ***.o: in function `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)':  
/***/boost/include/boost/spirit/home/support/utree/operators.hpp:545: multiple definition of `boost::spirit::utree boost::spirit::function_impl_invert::eval<bool>(bool const&)'; ***.o:/***/boost/include/boost/spirit/home/support/utree/operators.hpp:545: first defined here  
collect2: error: ld returned 1 exit status  
```
