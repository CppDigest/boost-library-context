# #36 - [Enhancement] Allow bounded queue size to exceed 65,535 for 64bit systems [Open]

> Username: yuvalif  
> Created at: 2017-08-14 09:48:09 UTC  
> Updated at: 2017-08-14 09:48:09 UTC  
> Url: https://github.com/boostorg/lockfree/issues/36  

in https://github.com/boostorg/lockfree/blob/develop/include/boost/lockfree/detail/freelist.hpp lines 335 and 369, replace the numeric const with compile time const derived from 32bit vs. 64bit compilation   
(e.g. `#if __x86_64__ || __ppc64__` )
