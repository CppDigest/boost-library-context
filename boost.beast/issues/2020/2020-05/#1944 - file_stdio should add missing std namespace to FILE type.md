# #1944 - file_stdio should add missing std namespace to FILE type [Closed]

> Username: spdw  
> Created at: 2020-05-06 16:39:34 UTC  
> Updated at: 2020-08-20 13:27:56 UTC  
> Closed at: 2020-05-20 14:18:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1944  

In Boost Beast `FILE` is used without `std` namespace, but the C++ header is included. Thus is does not compile for some operating systems.  
  
```  
boost/beast/core/file_stdio.hpp:29:5: error: 'FILE' does not name a type  
      FILE* f_ = nullptr;  
      ^  
```  
  
A change from  
```  
#include <cstdio>  
```  
to  
```  
#include <stdio.h>  
```  
would compile, but the C include is deprecated in C++. Therefore it is recommended to replace `FILE` with `std::FILE`. There seems to be 7 occurrences of `FILE`: 4 of them in `file_stdio.hpp` and 3 in the corresponding `file_stdio.ipp`.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-05-06 16:43:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1944#issuecomment-624760383  

@spdw Interesting. Do you know how well supported `std::FILE` is? We're still testing against clang 3.  
  
Would you be able to recommend a compiler/stdlib combination where this fails so I can add it to our test suite?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-05-06 16:53:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1944#issuecomment-624765588  

A fix for this is already in the works, see:  
https://github.com/boostorg/beast/pull/1906  
and  
https://github.com/vinniefalco/beast/commits/embarcadero

---

## Comment 3

> Username: spdw  
> Created at: 2020-05-06 19:09:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1944#issuecomment-624835373  

> A fix for this is already in the works, see:  
> #1906  
> and  
> https://github.com/vinniefalco/beast/commits/embarcadero  
  
To me it looks like the header file is adapted only, the ipp file on that branch still lacks the `std::` prefix for `FILE`.
