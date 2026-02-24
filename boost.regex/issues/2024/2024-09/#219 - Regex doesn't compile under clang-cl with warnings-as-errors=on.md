# #219 - Regex doesn't compile under clang-cl with warnings-as-errors=on [Closed]

> Username: pdimov  
> Created at: 2024-09-02 06:47:08 UTC  
> Updated at: 2024-09-03 10:29:57 UTC  
> Closed at: 2024-09-03 10:29:57 UTC  
> Url: https://github.com/boostorg/regex/issues/219  

E.g. https://github.com/boostorg/unordered/actions/runs/10655219740/job/29532497175?pr=277#step:6:489  
  
```  
compile-c-c++ bin.v2\libs\regex\build\clng-win-16.0.\dbg\x86_3\cxstd-14-iso\inln-on\optmz-spc\thrd-mlt\wide_posix_api.obj  
C:\actions-runner\_work\unordered\boost-root\libs\regex\build\..\src\wide_posix_api.cpp(199,21): error: 'wcscpy' is deprecated: This function or variable may be unsafe. Consider using wcscpy_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Werror,-Wdeprecated-declarations]  
               std::wcscpy(buf, localbuf);  
                    ^  
```  
  
This wasn't a problem before the modular changes because Serialization is referenced with `<warnings>off` here  
  
https://github.com/boostorg/unordered/blob/21937249c47da1fbdb445dc40775a88832fd772d/test/Jamfile.v2#L156  
  
However, post the modular changes Serialization depends on Regex, and `<warnings>off` doesn't get propagated to the Regex build for some reason, resulting in the error above.  
  
I see that a PR addressing this (https://github.com/boostorg/regex/pull/218) has already been merged, but it doesn't seem to help for some reason.  
  
The preprocessor condition should probably check for the MS STL, rather than specific compilers.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-09-02 06:58:44 UTC  
> Url: https://github.com/boostorg/regex/issues/219#issuecomment-2323957952  

Or maybe the file should just define `_CRT_SECURE_NO_WARNINGS` at the top and dispense with the #ifs.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-09-02 07:05:23 UTC  
> Url: https://github.com/boostorg/regex/issues/219#issuecomment-2323969083  

https://github.com/boostorg/regex/pull/220 implements the first suggested fix. I'll make a second PR for the second.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-09-02 07:10:14 UTC  
> Url: https://github.com/boostorg/regex/issues/219#issuecomment-2323977227  

https://github.com/boostorg/regex/pull/221 implements the second suggested fix.  
  
I've verified locally that both work.
