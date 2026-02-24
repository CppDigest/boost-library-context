# #29 use <iosfwd> instead of producing undefined behavior [Closed]

> Username: treh  
> Created at: 2016-08-25 07:27:34 UTC  
> Updated at: 2024-07-06 20:57:33 UTC  
> Closed at: 2024-07-06 20:57:33 UTC  
> Url: https://github.com/boostorg/interprocess/pull/29  

Hi Ion,  
  
I noticed you added the "pragma vtordisp" to fix the msvc bug. If we just include <iosfwd> we don't get any surprises should microsoft eventually remove the pragma from their headers again.  
Even worse: According to the standard it's undefined behavior to declare something in namespace std.  
  
Best,  
Tobi  
  
17.6.4.2.1 Namespace std [namespace.std]  
The behavior of a C++ program is undefined if it adds declarations or  
definitions to namespace std or to a namespace within namespace std  
unless otherwise specified.

---

## Comment 1

> Username: treh  
> Created_at: 2017-11-13 14:56:43 UTC  
> Url: https://github.com/boostorg/interprocess/pull/29#issuecomment-343943922  

Could you please reconsider merging this pull request?  
The current state can break any time, should Microsoft choose to change their standard library implementation. It's practically a bug waiting to happen.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2024-07-06 20:57:33 UTC  
> Url: https://github.com/boostorg/interprocess/pull/29#issuecomment-2211966087  

Thanks for the patch. Closing old issues which haven't received interest from users in recent years.

---
