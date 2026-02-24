# #75 - libboost_stacktrace_basic is conditioned on WinDbg [Closed]

> Username: pdimov  
> Created at: 2019-04-17 21:10:12 UTC  
> Updated at: 2019-04-18 23:42:13 UTC  
> Closed at: 2019-04-18 23:42:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/75  

https://github.com/boostorg/stacktrace/blob/1ad62e582aecb656faaf0c30fcf42fca4656cfbc/build/Jamfile.v2#L107

---

## Comment 1

> Username: pdimov  
> Created at: 2019-04-18 23:42:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/75#issuecomment-484724290  

Ah the condition here is reversed, `basic` builds when `WinDbg` doesn't build. Never mind.
