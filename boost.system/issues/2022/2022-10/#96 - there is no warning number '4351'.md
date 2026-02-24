# #96 - there is no warning number '4351' [Closed]

> Username: marakew  
> Created at: 2022-10-26 11:58:46 UTC  
> Updated at: 2022-10-27 14:32:04 UTC  
> Closed at: 2022-10-27 14:32:04 UTC  
> Url: https://github.com/boostorg/system/issues/96  

https://github.com/boostorg/system/blob/9a6d79b84147854aa919644b56b8553f5a2bedb8/include/boost/system/detail/error_category.hpp#L53  
  
MSVC seems unsupport this warning   
  
iam use MSVC 19/ 14.29.30133   
cl.exe  
Microsoft (R) C/C++ Optimizing Compiler Version 19.29.30146 for x64  
with -std:c++20  
  
so i got   
  
boost/system/detail/error_category.hpp(53): warning C4619: #pragma warning: there is no warning number '4351'

---

## Comment 1

> Username: pdimov  
> Created at: 2022-10-26 12:27:19 UTC  
> Url: https://github.com/boostorg/system/issues/96#issuecomment-1291957633  

I can't reproduce this. I'm trying the following program  
```  
#include <boost/system.hpp>  
#include <iostream>  
  
int main()  
{  
	std::cout << _MSC_FULL_VER << std::endl;  
}  
```  
under VS 2019, and there's no warning. (The output is `192930146`, so it's the same version of the compiler.)

---

## Comment 2

> Username: marakew  
> Created at: 2022-10-26 12:54:02 UTC  
> Url: https://github.com/boostorg/system/issues/96#issuecomment-1291990026  

yes i have the same _MSC_FULL_VER output  
let me do small research about warning

---

## Comment 3

> Username: marakew  
> Created at: 2022-10-26 13:09:59 UTC  
> Url: https://github.com/boostorg/system/issues/96#issuecomment-1292011198  

iam enable many warning for cl.exe  
so with /w14619  
i got - there is no warning number '4351'

---

## Comment 4

> Username: pdimov  
> Created at: 2022-10-26 13:19:43 UTC  
> Url: https://github.com/boostorg/system/issues/96#issuecomment-1292024167  

Yes, it looks like 4619 is disabled by default. There's another warning, 4616, for warning numbers that are even more invalid. :-)  
  
It seems that warning 4351 has been removed in VS 2015 and later, so I'll disable it only for earlier versions.

---

## Comment 5

> Username: marakew  
> Created at: 2022-10-26 14:18:50 UTC  
> Url: https://github.com/boostorg/system/issues/96#issuecomment-1292125882  

ok will wait fix in upstream

---

## Comment 6

> Username: pdimov  
> Created at: 2022-10-26 16:43:24 UTC  
> Url: https://github.com/boostorg/system/issues/96#issuecomment-1292320921  

Should be fixed with https://github.com/boostorg/system/commit/7ae6b317f3325bf722773ba7e5855c37811b3388.

---

## Comment 7

> Username: marakew  
> Created at: 2022-10-26 18:36:07 UTC  
> Url: https://github.com/boostorg/system/issues/96#issuecomment-1292443984  

i mean at master of boost  
https://github.com/boostorg/boost/tree/master/libs  
system @ 9a6d79b   8 days ago  
still not update

---

## Comment 8

> Username: pdimov  
> Created at: 2022-10-26 21:20:58 UTC  
> Url: https://github.com/boostorg/system/issues/96#issuecomment-1292665706  

Yes, I will update `master` as well, once CI passes on `develop`.

---

## Comment 9

> Username: marakew  
> Created at: 2022-10-27 14:29:23 UTC  
> Url: https://github.com/boostorg/system/issues/96#issuecomment-1293612243  

it seems ok, can be close issue
