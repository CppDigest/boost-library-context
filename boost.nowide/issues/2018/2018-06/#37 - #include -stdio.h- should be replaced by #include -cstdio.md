# #37 - #include <stdio.h> should be replaced by #include <cstdio> [Closed]

> Username: kivadiu  
> Created at: 2018-06-18 08:58:12 UTC  
> Updated at: 2018-06-18 09:09:52 UTC  
> Closed at: 2018-06-18 09:09:51 UTC  
> Url: https://github.com/boostorg/nowide/issues/37  

exists already in C++03 (and 98 I guess) so we can safely use it?  
include/boost/nowide/cstdio.hpp:12  
include/boost/nowide/filebuf.hpp:16  
src/iostream.cpp:11

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2018-06-18 09:09:51 UTC  
> Url: https://github.com/boostorg/nowide/issues/37#issuecomment-397990619  

The functions I need are `_wfopen` and so, they are by no means parts of standard C++03. So OS `stdio.h` is the correct header
