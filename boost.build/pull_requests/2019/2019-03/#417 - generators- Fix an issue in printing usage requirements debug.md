# #417 generators: Fix an issue in printing usage requirements debug. [Merged]

> Username: tee3  
> Created at: 2019-03-25 15:26:48 UTC  
> Updated at: 2021-10-02 21:19:03 UTC  
> Merged at: 2019-04-03 16:19:28 UTC  
> Closed at: 2019-04-03 16:19:28 UTC  
> Url: https://github.com/boostorg/build/pull/417  

This pull request fixes a bug in printing usage requirements when the `--debug-generators` option is enabled.  
  
I've attached a zip file with example Boost.Build project and logs.  
  
```  
b2 --debug-generators -a  
```  
  
The difference in the logs is shown below.  
  
```diff  
97c97  
<                with usage requirements:  
---  
>                with usage requirements: [ <relevant>address-model <relevant>architecture <relevant>cflags <relevant>debug-symbols <relevant>define <relevant>flags <relevant>include <relevant>inlining <relevant>instruction-set <relevant>link <relevant>local-visibility <relevant>optimization <relevant>pch <relevant>pch-file <relevant>profiling <relevant>target-os <relevant>threading <relevant>toolset <relevant>toolset-clang:platform <relevant>toolset-clang:version <relevant>warnings <relevant>warnings-as-errors ]  
107c107  
<            with usage requirements:  
---  
>            with usage requirements: [ <relevant>address-model <relevant>architecture <relevant>cflags <relevant>cxxstd <relevant>cxxstd-dialect <relevant>debug-symbols <relevant>define <relevant>dll-path <relevant>find-shared-library <relevant>find-static-library <relevant>flags <relevant>include <relevant>inlining <relevant>instruction-set <relevant>library-file <relevant>library-path <relevant>link <relevant>linkflags <relevant>local-visibility <relevant>optimization <relevant>pch <relevant>pch-file <relevant>profiling <relevant>runtime-link <relevant>strip <relevant>target-os <relevant>threading <relevant>toolset <relevant>toolset-clang:platform <relevant>toolset-clang:version <relevant>warnings <relevant>warnings-as-errors <relevant>xdll-path <xdll-path>/private/tmp/tmp/bin/clang-darwin-10.0/debug ]  
111c111  
<        with usage requirements:  
---  
>        with usage requirements: [ <relevant>address-model <relevant>architecture <relevant>cflags <relevant>cxxstd <relevant>cxxstd-dialect <relevant>debug-symbols <relevant>define <relevant>dll-path <relevant>find-shared-library <relevant>find-static-library <relevant>flags <relevant>include <relevant>inlining <relevant>instruction-set <relevant>library-file <relevant>library-path <relevant>link <relevant>linkflags <relevant>local-visibility <relevant>optimization <relevant>pch <relevant>pch-file <relevant>profiling <relevant>runtime-link <relevant>strip <relevant>target-os <relevant>threading <relevant>toolset <relevant>toolset-clang:platform <relevant>toolset-clang:version <relevant>warnings <relevant>warnings-as-errors <relevant>xdll-path <xdll-path>/private/tmp/tmp/bin/clang-darwin-10.0/debug ]  
140c140  
<                with usage requirements:  
---  
>                with usage requirements: [ <relevant>address-model <relevant>architecture <relevant>cflags <relevant>debug-symbols <relevant>define <relevant>flags <relevant>include <relevant>inlining <relevant>instruction-set <relevant>link <relevant>local-visibility <relevant>optimization <relevant>pch <relevant>pch-file <relevant>profiling <relevant>target-os <relevant>threading <relevant>toolset <relevant>toolset-clang:platform <relevant>toolset-clang:version <relevant>warnings <relevant>warnings-as-errors ]  
150c150  
<            with usage requirements:  
---  
>            with usage requirements: [ <relevant>address-model <relevant>architecture <relevant>cflags <relevant>cxxstd <relevant>cxxstd-dialect <relevant>debug-symbols <relevant>define <relevant>dll-path <relevant>find-shared-library <relevant>find-static-library <relevant>flags <relevant>include <relevant>inlining <relevant>instruction-set <relevant>library-file <relevant>library-path <relevant>link <relevant>linkflags <relevant>local-visibility <relevant>optimization <relevant>pch <relevant>pch-file <relevant>profiling <relevant>runtime-link <relevant>strip <relevant>target-os <relevant>threading <relevant>toolset <relevant>toolset-clang:platform <relevant>toolset-clang:version <relevant>warnings <relevant>warnings-as-errors <relevant>xdll-path <xdll-path>/private/tmp/tmp/bin/clang-darwin-10.0/debug ]  
154c154  
<        with usage requirements:  
---  
>        with usage requirements: [ <relevant>address-model <relevant>architecture <relevant>cflags <relevant>cxxstd <relevant>cxxstd-dialect <relevant>debug-symbols <relevant>define <relevant>dll-path <relevant>find-shared-library <relevant>find-static-library <relevant>flags <relevant>include <relevant>inlining <relevant>instruction-set <relevant>library-file <relevant>library-path <relevant>link <relevant>linkflags <relevant>local-visibility <relevant>optimization <relevant>pch <relevant>pch-file <relevant>profiling <relevant>runtime-link <relevant>strip <relevant>target-os <relevant>threading <relevant>toolset <relevant>toolset-clang:platform <relevant>toolset-clang:version <relevant>warnings <relevant>warnings-as-errors <relevant>xdll-path <xdll-path>/private/tmp/tmp/bin/clang-darwin-10.0/debug ]  
238c238  
<            with usage requirements:  
---  
>            with usage requirements: [ <relevant>address-model <relevant>architecture <relevant>cflags <relevant>debug-symbols <relevant>define <relevant>flags <relevant>include <relevant>inlining <relevant>instruction-set <relevant>link <relevant>local-visibility <relevant>optimization <relevant>pch <relevant>pch-file <relevant>profiling <relevant>target-os <relevant>threading <relevant>toolset <relevant>toolset-clang:platform <relevant>toolset-clang:version <relevant>warnings <relevant>warnings-as-errors ]  
248c248  
<        with usage requirements:  
---  
>        with usage requirements: [ <relevant>address-model <relevant>architecture <relevant>cflags <relevant>cxxstd <relevant>cxxstd-dialect <relevant>debug-symbols <relevant>define <relevant>dll-path <relevant>find-shared-library <relevant>find-static-library <relevant>flags <relevant>include <relevant>inlining <relevant>instruction-set <relevant>library-file <relevant>library-path <relevant>link <relevant>linkflags <relevant>local-visibility <relevant>optimization <relevant>pch <relevant>pch-file <relevant>profiling <relevant>runtime-link <relevant>strip <relevant>target-os <relevant>threading <relevant>toolset <relevant>toolset-clang:platform <relevant>toolset-clang:version <relevant>warnings <relevant>warnings-as-errors <relevant>xdll-path <xdll-path>/private/tmp/tmp/bin/clang-darwin-10.0/debug ]  
```  
  
[example.zip](https://github.com/boostorg/build/files/3039644/example.zip)

---

## Comment 1

> Username: tee3  
> Created_at: 2019-04-03 16:11:22 UTC  
> Url: https://github.com/boostorg/build/pull/417#issuecomment-479556576  

I added a description and an example that shows the bug.

---
