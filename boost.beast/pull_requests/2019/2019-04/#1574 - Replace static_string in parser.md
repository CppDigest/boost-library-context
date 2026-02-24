# #1574 Replace static_string in parser [Closed]

> Username: djarek  
> Created at: 2019-04-15 11:39:53 UTC  
> Updated at: 2019-04-16 17:20:41 UTC  
> Closed at: 2019-04-15 16:04:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1574  

This change yields a modest performance improment of 1-2% by replacing the exception-based handling of buffer overflow with one based on regular conditional checks.

---
