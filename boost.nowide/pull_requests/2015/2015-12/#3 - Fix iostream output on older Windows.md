# #3 Fix iostream output on older Windows [Merged]

> Username: MikaelSmith  
> Created at: 2015-12-07 18:40:30 UTC  
> Updated at: 2015-12-07 18:44:59 UTC  
> Merged at: 2015-12-07 18:43:41 UTC  
> Closed at: 2015-12-07 18:43:41 UTC  
> Url: https://github.com/boostorg/nowide/pull/3  

WriteConsoleW originally required the lpNumberOfCharsWritten argument.  
That seems to have changed in later releases, but is required for  
Windows Server 2003 and 2008. Fix a seg fault by passing the required  
argument.

---
