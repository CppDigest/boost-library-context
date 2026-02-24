# #428 - NVIDIA C++ compiler -cuda option triggers incompatible redefiniton of BOOST_COMPILER [Open]

> Username: olupton  
> Created at: 2022-04-28 08:10:05 UTC  
> Updated at: 2022-04-28 08:10:05 UTC  
> Url: https://github.com/boostorg/config/issues/428  

Passing the `-cuda` option to `nvc++` makes it define the `__CUDACC__` macro, which in turn leads to a warning from Boost:  
```  
"config/include/boost/config/compiler/pgi.hpp", line 12: warning: incompatible redefinition of macro "BOOST_COMPILER" (declared at line 11 of "config/include/boost/config/compiler/nvcc.hpp")  
  #define BOOST_COMPILER "PGI compiler version " BOOST_STRINGIZE(BOOST_COMPILER_VERSION)  
          ^  
```  
(tested with today's `develop`, c11afa5ffa7c8612cf8ae32ed65be3b2863153a9, and the 1.78 release).  
  
`nvc++` also defines macros such as `__PGI` and `__NVCOMPILER`.
