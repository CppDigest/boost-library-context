# #55 - BOOST_OVERRIDE missing [Closed]

> Username: ElDuderinoBerlin  
> Created at: 2022-11-14 23:32:44 UTC  
> Updated at: 2022-11-15 02:44:54 UTC  
> Closed at: 2022-11-15 02:44:54 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/55  

Error:  
d:\boost_1_80_0\msvc-14.3-64\boost\lexical_cast\bad_lexical_cast.hpp(54,60): error C3646: 'BOOST_OVERRIDE': unknown override specifier  
  
My temp fix:  
  
#ifndef BOOST_OVERRIDE  
#define BOOST_OVERRIDE override  
#endif

---

## Comment 1

> Username: mclow  
> Created at: 2022-11-15 00:00:41 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/55#issuecomment-1314567361  

I tried this on godbolt with a reasonably new MSVC:  
  
```  
#include <boost/lexical_cast/bad_lexical_cast.hpp>  
  
int main () {}  
```  
  
I don't know what MSVC 14.3 is.  Can you give me more information about it?  
it's not listed on the [MSVC Wikipedia page](https://en.wikipedia.org/wiki/Microsoft_Visual_C%2B%2B)

---

## Comment 2

> Username: ElDuderinoBerlin  
> Created at: 2022-11-15 01:28:05 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/55#issuecomment-1314632192  

> I tried this on godbolt with a reasonably new MSVC:  
>   
> ```  
> #include <boost/lexical_cast/bad_lexical_cast.hpp>  
>   
> int main () {}  
> ```  
>   
  
Yes this works for a simple console app. Must be includes of mine which in conjunction with boost produces this error.  
I fixed this by just excluding  "lexical_cast.hpp" which wasn't really needed i figured out.  
  
I close this for now.
