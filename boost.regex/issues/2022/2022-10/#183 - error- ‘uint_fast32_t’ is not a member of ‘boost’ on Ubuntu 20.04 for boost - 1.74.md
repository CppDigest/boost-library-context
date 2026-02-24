# #183 - error: ‘uint_fast32_t’ is not a member of ‘boost’ on Ubuntu 20.04 for boost > 1.74 [Closed]

> Username: HFTrader  
> Created at: 2022-10-14 18:32:17 UTC  
> Updated at: 2022-12-03 09:22:35 UTC  
> Closed at: 2022-12-03 09:22:35 UTC  
> Url: https://github.com/boostorg/regex/issues/183  

The build succeeds for all versions up to 1.74.0 but fails onwards with the message  
```bash  
gcc.compile.c++ bin.v2/libs/regex/build/gcc-9/release/link-static/visibility-hidden/posix_api.o  
libs/regex/build/../src/posix_api.cpp: In function ‘int boost::regcompA(boost::regex_tA*, const char*, int)’:  
libs/regex/build/../src/posix_api.cpp:90:11: error: ‘uint_fast32_t’ is not a member of ‘boost’  
   90 |    boost::uint_fast32_t flags = (f & REG_PERLEX) ? 0 : ((f & REG_EXTENDED) ? regex::extended : regex::basic);  
```  
This gist is a minimum reproducible example: https://gist.github.com/HFTrader/689df916ea816e52e673905f82cccfda  
  
Change `TAG` from `boost-1.78.0` to `boost-1.71.0` and it compiles.   
  
Tested on GCC (stock), clang (stock) and clang 14.0.6 on Ubuntu 20.04 with the same outcome.
