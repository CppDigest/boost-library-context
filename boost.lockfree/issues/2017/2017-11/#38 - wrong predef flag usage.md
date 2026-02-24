# #38 - wrong predef flag usage [Open]

> Username: zhenlw  
> Created at: 2017-11-04 02:55:48 UTC  
> Updated at: 2017-11-04 02:55:48 UTC  
> Url: https://github.com/boostorg/lockfree/issues/38  

Working on a new boost release I found the lockfree queue stopped working proper and tracked it down: some x86_64 codes are included improperly in a couple of files, namely in boost/lockfree/detail/prefix.hpp and tagged_ptr_ptrcompression.hpp:  
  
    #ifdef BOOST_ARCH_X86_64  
  
Should be  
  
    #if BOOST_ARCH_X86_64  
  
Otherwise codes on all the other ARCHs will include the wrong piece. I tested the change on powerpc and it works fine so far.
