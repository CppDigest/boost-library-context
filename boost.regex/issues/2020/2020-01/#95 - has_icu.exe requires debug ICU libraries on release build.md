# #95 - has_icu.exe requires debug ICU libraries on release build [Open]

> Username: chr-thien  
> Created at: 2020-01-09 08:29:02 UTC  
> Updated at: 2020-01-22 18:17:42 UTC  
> Url: https://github.com/boostorg/regex/issues/95  

I'm building the release variant of Boost 1.72 using MSVC 14.  
I would like to link Regex to ICU.  
ICU has standard library names.  
ICU release build libraries are located in lib64 subdirectory of ICU_PATH parameter.  
There are no debug build libraries of ICU.  
  
`b2.exe --reconfigure --prefix=<path> --layout=versioned --with-regex -j4 -sICU_PATH=<path> toolset=msvc-14.0 address-model=64 link=shared runtime-link=shared variant=release inlining=off optimization=off debug-symbols=on install`  
  
During release build ICU is not found because it requires debug libraries of ICU to be present.  
has_icu.exe is always built as debug variant and therefore wants to link to icuucd, icuind.  
  
The release build should use release libraries of ICU for all targets.  
  
I know there is ICU_ICUUC_NAME for overriding. But when using standard library names the automatic detection should work reliably.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-01-22 18:17:41 UTC  
> Url: https://github.com/boostorg/regex/issues/95#issuecomment-577317062  

Moved upstream to Boost.Build.
