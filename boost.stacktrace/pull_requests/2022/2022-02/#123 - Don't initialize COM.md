# #123 Don't initialize COM [Merged]

> Username: AlexGuteniev  
> Created at: 2022-02-18 17:47:36 UTC  
> Updated at: 2022-09-01 14:50:41 UTC  
> Merged at: 2022-09-01 14:42:13 UTC  
> Closed at: 2022-09-01 14:42:13 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/123  

Resolve #121  
  
See the [documentation](https://docs.microsoft.com/windows-hardware/drivers/ddi/dbgeng/nf-dbgeng-debugcreate#remarks):   
  
> You don't need to call **CoInitialize**, **CoInitializeEx**, or **OleInitialize** to use this function and interfaces obtained by it.  
  
Even if DIA support is implemented, there's a way to avoid `CoInitializeEx` for DIA too.

---

## Comment 1

> Username: AlexGuteniev  
> Created_at: 2022-02-21 08:57:52 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/123#issuecomment-1046620083  

BTW if DbgEng interfaces were COM apartment aware, this comment is wrong:  
> ```  
>         // COINIT_MULTITHREADED means that we must serialize access to the objects manually.  
>         // This is the fastest way to work.  
> ```  
  
`COINIT_MULTITHREADED` does not always mean better perf (in particular it is worse when the underlying server is apartment-threaded), and does not ever mean that the synchronization to call COM methods is necessary (it rather means that the  synchronization often is needed _inside_ COM methods, as they are called from arbitrary threads).

---

## Comment 2

> Username: apolukhin  
> Created_at: 2022-09-01 14:43:03 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/123#issuecomment-1234380610  

Many many thanks for the great news and the PR!

---
