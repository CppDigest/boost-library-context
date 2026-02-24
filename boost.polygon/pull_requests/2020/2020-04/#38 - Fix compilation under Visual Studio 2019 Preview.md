# #38 Fix compilation under Visual Studio 2019 Preview. [Closed]

> Username: helixhorned  
> Created at: 2020-04-15 11:41:01 UTC  
> Updated at: 2020-07-09 10:22:13 UTC  
> Closed at: 2020-04-15 19:13:23 UTC  
> Url: https://github.com/boostorg/polygon/pull/38  

(Version 16.6.0 Preview 2.1, MSVC 14.26.28720)  
  
Apparently, it considers C++20 (n4835) 9.2.3 [dcl.typedef] #10 even  
though that paragraph does not appear in the C++17 Standard (draft).  
  
----  
  
This is an odd one. I am compiling as C++17, so as far as I can see it should not trigger. It seems that MSVC chooses to implement the restriction of the mentioned paragraph in a wider scope than just C++20.  
  
As far as I can see, source compatibility should not be affected since the two unnamed classes are only used in private contexts. However:  
  
**CAUTION**: I don't fully know the implications on binary compatibility. (Just, presumably, that this change may break ABI if typedef'd unnamed classes are mangled differently than if you give the class the name directly.)

---

## Comment 1

> Username: glenfe  
> Created_at: 2020-04-15 19:13:23 UTC  
> Updated_at: 2020-04-15 19:19:03 UTC  
> Url: https://github.com/boostorg/polygon/pull/38#issuecomment-614228208  

@helixhorned Thanks - addressed in https://github.com/boostorg/polygon/commit/5cfcb6cad08dd5413f4a97af6a4fe7c35361de67 . I went ahead and changed the other `typedef struct` and `typedef class` cases too.

---

## Comment 2

> Username: yoavmil  
> Created_at: 2020-07-09 10:22:13 UTC  
> Url: https://github.com/boostorg/polygon/pull/38#issuecomment-656044196  

For future Google searches, this happened to me too:  
  
> boost\polygon\voronoi_diagram.hpp(266,17): error C5208: unnamed class used in typedef name cannot declare members other than non-static data members, member enumerations, or member classes  
  
I could workaround it by forcing VS to ignore those warning, see [here](https://developercommunity.visualstudio.com/content/problem/1034754/warning-c5208-a-c20-feature-occurs-when-compiling-1.html).  
  
Add `#pragma warning( disable : 5208)` before you include the Voronoi headers

---
