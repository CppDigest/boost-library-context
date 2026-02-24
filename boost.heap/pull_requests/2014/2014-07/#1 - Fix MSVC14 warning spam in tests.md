# #1 Fix MSVC14 warning spam in tests [Merged]

> Username: MarcelRaad  
> Created at: 2014-07-03 16:35:30 UTC  
> Updated at: 2014-07-03 17:39:41 UTC  
> Merged at: 2014-07-03 17:38:18 UTC  
> Closed at: 2014-07-03 17:38:18 UTC  
> Url: https://github.com/boostorg/heap/pull/1  

Microsoft Visual C++ 14 has new variable shadowing warnings. The test output is full of  
"warning C4456: declaration of 'i' hides previous local declaration".

---
