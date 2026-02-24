# #14 - Possible crash in putenv [Closed]

> Username: artyom-beilis  
> Created at: 2017-06-14 05:04:02 UTC  
> Updated at: 2018-06-18 09:22:51 UTC  
> Closed at: 2018-06-18 09:22:51 UTC  
> Url: https://github.com/boostorg/nowide/issues/14  

> In file included from test\test_system.cpp:11:0:  
> ..\../boost/nowide/cenv.hpp: In function 'int boost::nowide::putenv(char*)':  
> ..\../boost/nowide/cenv.hpp:104:41: warning: comparison between pointer and  
> zero character constant [-Wpointer-compare]  
>         while(*key_end!='=' && key_end!='\0')

---

## Comment 1

> Username: lordofhyphens  
> Created at: 2017-10-18 15:21:46 UTC  
> Url: https://github.com/boostorg/nowide/issues/14#issuecomment-337627818  

If it's possible to propagate a null pointer to this function, then there should also be a check for it.  
  
The crash here looks like it will stem from the pointer going out on a journey and not coming back until the segfault comes.

---

## Comment 2

> Username: artyom-beilis  
> Created at: 2018-06-18 09:22:49 UTC  
> Url: https://github.com/boostorg/nowide/issues/14#issuecomment-397994033  

Fixed in review branch
