# #37 - Many -Wdeprecated-variadic-comma-omission warnings in preprocessed files in C++26 [Open]

> Username: Romain-Geissler-1A  
> Created at: 2025-06-27 19:33:45 UTC  
> Updated at: 2025-06-27 19:34:31 UTC  
> Url: https://github.com/boostorg/proto/issues/37  

Hi,  
  
When trying to use Boost.Proto (indirectly, through Boost.Xpressive), using gcc 15, C++ 26, and flags `-Wall -Wextra -Werror` we do get many -Wdeprecated-variadic-comma-omission warnings in preprocessed files (and possibly also in other files, I didn't check).  
  
Here is the first warning I get:  
  
```  
/data/mwrep/res/osp/Boost/25-0-0-0/include/boost/proto/transform/detail/preprocessed/call.hpp:12:23: error: omission of ',' before varargs '...' is deprecated in C++26 [-Werror=deprecated-variadic-comma-omission]  
   12 |     struct call<Fun(A0...)> : transform<call<Fun(A0...)> >  
      |                       ^~~  
      |  
```  
  
This is probably easily fixable by updating the wave rules to generate these, files, but I have honestly no idea how to even use this Boost.Wave thing (and I am not sure these preprocessing still make much sense in a post C++11 era) ;)
