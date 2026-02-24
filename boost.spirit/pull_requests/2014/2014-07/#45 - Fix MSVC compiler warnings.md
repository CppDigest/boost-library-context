# #45 Fix MSVC compiler warnings [Merged]

> Username: MarcelRaad  
> Created at: 2014-07-05 18:14:52 UTC  
> Updated at: 2014-07-07 04:39:02 UTC  
> Merged at: 2014-07-05 21:42:48 UTC  
> Closed at: 2014-07-05 21:42:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/45  

This fixes the following warnings on Microsoft Visual C++:  
"warning C4800: 'int': forcing value to bool 'true' or 'false' (performance warning)"  
"warning C4456: declaration of 'regex' hides previous local declaration"  
"warning C4456: declaration of 'index_' hides previous local declaration"  
The first one occurs in all versions, the shadowing warnings are new in MSVC 14.

---
