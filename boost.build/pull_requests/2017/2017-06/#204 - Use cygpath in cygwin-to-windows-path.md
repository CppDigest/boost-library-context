# #204 Use cygpath in cygwin-to-windows-path [Merged]

> Username: pdimov  
> Created at: 2017-06-14 00:44:16 UTC  
> Updated at: 2021-10-02 21:00:10 UTC  
> Merged at: 2017-06-14 23:49:45 UTC  
> Closed at: 2017-06-14 23:49:45 UTC  
> Url: https://github.com/boostorg/build/pull/204  

The current logic in `cygwin.cygwin-to-windows-path` doesn't work for me. The path to `ar` that `gcc -print-prog-name=ar` gives is `/usr/lib/gcc/i686-pc-cygwin/6.3.0/../../../../i686-pc-cygwin/bin/ar.exe`, with a Windows equivalent of `C:\cygwin\usr\i686-pc-cygwin\bin\ar.exe`, but the code yields `\usr\i686-pc-cygwin\bin\ar.exe`, which fails.  
  
This patch uses `cygpath -w` instead, which works in my case.

---

## Comment 1

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:00:10 UTC  
> Url: https://github.com/boostorg/build/pull/204#issuecomment-932819755  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
