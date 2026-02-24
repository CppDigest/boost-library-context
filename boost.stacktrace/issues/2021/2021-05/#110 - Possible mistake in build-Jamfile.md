# #110 - Possible mistake in build/Jamfile? [Closed]

> Username: pdimov  
> Created at: 2021-05-28 03:15:39 UTC  
> Updated at: 2021-06-01 00:27:24 UTC  
> Closed at: 2021-06-01 00:27:24 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/110  

Is this line correct? https://github.com/boostorg/stacktrace/blob/b37e0d8e9f9613813534b6765559bdec693f34d5/build/Jamfile.v2#L107  
  
stacktrace_basic should not require WinDbg.

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-05-28 08:30:36 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/110#issuecomment-850249100  

It is fine. It disables builds of stacktrace_basic if WinDbg is in there
