# #2 Avoid MSVC warning in tests [Merged]

> Username: MarcelRaad  
> Created at: 2014-07-05 15:34:19 UTC  
> Updated at: 2014-07-07 09:03:40 UTC  
> Merged at: 2014-07-07 09:02:04 UTC  
> Closed at: 2014-07-07 09:02:04 UTC  
> Url: https://github.com/boostorg/any/pull/2  

Visual C++ emits the following warning on implicit conversions from pointers to bool:  
"warning C4800: 'const void *': forcing value to bool 'true' or 'false' (performance warning)"  
Fixed by explicitly comparing the pointer to 0.

---
