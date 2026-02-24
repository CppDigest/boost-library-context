# #23 - valgrind warning when allocating large amount of memory [Open]

> Username: yuvalif  
> Created at: 2019-05-25 06:22:37 UTC  
> Updated at: 2019-05-25 06:22:37 UTC  
> Url: https://github.com/boostorg/multi_array/issues/23  

The following program:  
```  
#include "boost/multi_array.hpp"  
using Emax = boost::multi_array<double, 7>;   
  
int main() {  
    Emax emax(boost::extents[40][30][50][5][5][5][5]);  
    return 0;  
}  
```  
when run under valgrind gives the following warnings:  
```  
==20786== Memcheck, a memory error detector  
==20786== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.  
==20786== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info  
==20786== Command: ./test_arr  
==20786==  
==20786== Warning: set address range perms: large range [0x5171040, 0x16f8b340) (undefined)  
==20786== Warning: set address range perms: large range [0x5171028, 0x16f8b358) (noaccess)  
==20786==  
==20786== HEAP SUMMARY:  
==20786==     in use at exit: 0 bytes in 0 blocks  
==20786==   total heap usage: 2 allocs, 2 frees, 300,072,704 bytes allocated  
==20786==  
==20786== All heap blocks were freed -- no leaks are possible  
==20786==  
==20786== For lists of detected and suppressed errors, rerun with: -s  
==20786== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)  
```  
versions are:  
```  
gcc version 8.3.1 20190223 (Red Hat 8.3.1-2) (GCC)  
valgrind-3.15.0  
```
