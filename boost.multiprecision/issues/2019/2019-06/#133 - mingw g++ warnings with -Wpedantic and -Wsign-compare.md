# #133 - mingw g++ warnings with -Wpedantic and -Wsign-compare [Closed]

> Username: o2nwr  
> Created at: 2019-06-19 01:12:00 UTC  
> Updated at: 2019-06-29 17:57:15 UTC  
> Closed at: 2019-06-29 17:57:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/133  

Building simple program that includes boost/multiprecision/mpc.hpp with mingw32-g++ with -Wall -Wpedantic yields these warnings:  
```  
...\boost_1_70_0/boost/multiprecision/debug_adaptor.hpp:425:29: warning: extra ';' [-Wpedantic]  
 NON_MEMBER_OP4(powm, "powm");  
                             ^  
  
...\boost_1_70_0/boost/multiprecision/mpc.hpp: In member function 'boost::multiprecision::backends::mpc_complex_backend<0>& boost::multiprecision::backends::mpc_complex_backend<0>::operator=(mpf_srcptr)':  
...\boost_1_70_0/boost/multiprecision/mpc.hpp:558:32: warning: comparison of integer expressions of different signedness: 'mpfr_prec_t' {aka 'long int'} and 'mp_bitcnt_t' {aka 'long unsigned int'} [-Wsign-compare]  
       if (mpc_get_prec(data()) != mpf_get_prec(val))  
           ~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~  
```  
```  
C:\MinGW\bin>mingw32-g++.exe --version  
mingw32-g++.exe (MinGW.org GCC-8.2.0-3) 8.2.0  
Copyright (C) 2018 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```
