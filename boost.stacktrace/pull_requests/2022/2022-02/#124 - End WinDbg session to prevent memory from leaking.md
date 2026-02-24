# #124 End WinDbg session to prevent memory from leaking [Closed]

> Username: akarpovskii  
> Created at: 2022-02-18 18:01:34 UTC  
> Updated at: 2024-02-29 17:22:55 UTC  
> Closed at: 2024-02-29 17:22:54 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/124  

If **BOOST_STACKTRACE_USE_WINDBG** is defined, the implementation of `stacktrace::to_string` opens a new WinDbg session each time without ending the previous one which results in leaking memory (see #111).  
  
This PR adds the missing [EndSession](https://docs.microsoft.com/en-us/windows-hardware/drivers/ddi/dbgeng/nf-dbgeng-idebugclient-endsession) call in the `debugging_symbols` destructor.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2023-01-18 15:59:16 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/124#issuecomment-1387306583  

@akarpovskii , it looks like path breaks something. See https://github.com/boostorg/stacktrace/pull/130 the CI failures  
  
The MSVC STL implementation also does not do the EndSession https://github.com/microsoft/STL/blob/main/stl/src/stacktrace.cpp

---

## Comment 2

> Username: akarpovskii  
> Created_at: 2023-02-02 19:05:17 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/124#issuecomment-1414225525  

@apolukhin, sorry for leaving the PR unfinished. At the time I didn't have enough expertise to figure this out, and now I don't have access to a Windows machine to finish it.  
I hope this pull request will serve as a foundation for future fixes.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2024-02-29 17:22:55 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/124#issuecomment-1971610685  

Fixed in https://github.com/boostorg/stacktrace/commit/27093f24cb2ff187b179ffc561bdf0c27d7cf551 and https://github.com/boostorg/stacktrace/commit/f783534b0f22379e39597cf380f01e7199c24c29

---
