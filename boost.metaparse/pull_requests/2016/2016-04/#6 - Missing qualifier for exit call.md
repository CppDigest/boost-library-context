# #6 Missing qualifier for exit call. [Merged]

> Username: akumta  
> Created at: 2016-04-08 21:07:22 UTC  
> Updated at: 2016-04-09 10:47:42 UTC  
> Merged at: 2016-04-09 10:47:42 UTC  
> Closed at: 2016-04-09 10:47:42 UTC  
> Url: https://github.com/boostorg/metaparse/pull/6  

libs/metaparse/example/parsing_error/main.cpp fails to compile with Oracle Solaris Studio12.5.  
It would be great if this can get in for Boost 1.61.

---

## Comment 1

> Username: akumta  
> Created_at: 2016-04-08 22:01:25 UTC  
> Url: https://github.com/boostorg/metaparse/pull/6#issuecomment-207624127  

Here is the error currently seen:  
/CC -library=stlport4 -xO4 -mt -erroff=%none -m32 -KPIC -DBOOST_ALL_NO_LIB=1 -DNDEBUG -I"../" -c -o ./main.o ../libs/metaparse/example/parsing_error/main.cpp  
  
"../boost/metaparse/v1/debug_parsing_error.hpp", line 45: Error: The function "exit" must have a prototype.  
"../libs/metaparse/example/parsing_error/main.cpp", line 43:     Where: While instantiating "boost::metaparse::v1::debug_parsing_errorboost::metaparse::v1::sequence<boost::metaparse::v1::repeated<boost::metaparse::v1::lit_c<97>, boost::metaparse::v1::lit_c<98>, mpl_::na, mpl_::na, mpl_::na>, boost::metaparse::v1::string<97, 97, 97, 99, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1>>::debug_parsing_error()".  
"../libs/metaparse/example/parsing_error/main.cpp", line 43:     Where: Instantiated from non-template code.  
1 Error(s) detected.

---
