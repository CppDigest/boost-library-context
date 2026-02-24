# #502 Add proper armasm[64] support for MSVC toolchain [Merged]

> Username: janisozaur  
> Created at: 2019-11-04 22:37:28 UTC  
> Updated at: 2021-10-02 21:13:28 UTC  
> Merged at: 2019-11-14 22:24:43 UTC  
> Closed at: 2019-11-14 22:24:43 UTC  
> Url: https://github.com/boostorg/build/pull/502  

armasm and armasm64, the MSVC toolchain's assemblers for arm and arm64  
respectively, have completely different invocations than their ml and  
ml64 counterparts targetting x86 and x86-64.  
  
Here's the full output of armasm's help:  
```  
cmd> armasm64 -h  
Microsoft (R) ARM Macro Assembler Version 14.23.28106.4 for 64 bits  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
 Usage:      armasm [<options>] sourcefile objectfile  
             armasm [<options>] -o objectfile sourcefile  
             armasm -h              for help  
  
<options>:            (Upper case shows allowable abbreviation)  
  -Errors     errorsfile       redirect stderr diagnostics to errorsfile  
  -I          dir[;dir]        add dirs to include search path  
  -PreDefine  directive        pre-execute a SET{L,A,S} directive  
  -NOWarn                      turn off warning messages  
  -ignore <warning-num>        don't report warning-num  
  -Help                        help (this information)  
  -via <file>                  read further arguments from <file>  
  -machine <machine>           set the PE machine type field  
  -g                           generate debugging info  
  -gh:SHA_256                  use SHA256 for file checksum in debug info (experimental)  
  -errorReport:<option>        report internal assembler errors to Microsoft  
      none - do not send report  
      prompt - prompt to immediately send report  
      queue - at next admin logon, prompt to send report (default)  
      send - send report automatically  
  
<machine>:  ARM64  
```  
  
This patch is incomplete, as I struggle to work with JAM and would like to request some help. I have verified this was sufficient to ~~compile~~ assemble an object file with b2 targetting arm64 with this patch.  
  
Things to address:  
- [x] This updated definition needs to be only enabled for arm/arm64.  
  - I tried making the action depend on `<architecture>` but couldn't find the proper way to express the condition.  
  - I'm not sure if it is the best approach, the only other such top-level conditions check for `os.name`  
  - I haven't seen conditional `generator.override`s either  
- [x] The `<ASMFLAGS>` in https://github.com/boostorg/build/blob/e04b0c206e5d09e25262bed914e30324facdf9cb/src/tools/msvc.jam#L1864 may need only setting for x86-like targets, rather than outright removing them

---

## Comment 1

> Username: janisozaur  
> Created_at: 2019-11-06 22:11:21 UTC  
> Url: https://github.com/boostorg/build/pull/502#issuecomment-550524118  

I was able to address the shortcomings of my initial approach and provide a solution that works for both, `ml64` and its siblings as well as `armasm64` family. This is greatly due to help from @grisumbras .  
  
The patch provided here is proven to work for:  
* x86: https://github.com/janisozaur/vcpkg/commit/03484a03a4709ac98f743c3f3f72b46d9f935eb9/checks?check_suite_id=299233094  
* x86_64: https://github.com/janisozaur/vcpkg/commit/03484a03a4709ac98f743c3f3f72b46d9f935eb9/checks?check_suite_id=299233068  
* arm64: https://github.com/janisozaur/vcpkg/commit/03484a03a4709ac98f743c3f3f72b46d9f935eb9/checks?check_suite_id=299233078  
  
The last job targetting arm64 is using an upcoming patch to Boost.Context

---

## Review 2 [Changes requested]

> Username: grafikrobot  
> Created_at: 2019-11-14 18:10:54 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/502#pullrequestreview-317157141  

Looks good. One minor change.. Could you add yourself to the copyrights as there's enough here to qualify as significant.

---

## Comment 3

> Username: janisozaur  
> Created_at: 2019-11-14 21:09:44 UTC  
> Url: https://github.com/boostorg/build/pull/502#issuecomment-554080449  

Updated

---

## Comment 4

> Username: janisozaur  
> Created_at: 2019-11-14 21:46:44 UTC  
> Url: https://github.com/boostorg/build/pull/502#issuecomment-554095044  

`timedata` test of MSVC 2013 test suite failed, but this doesn't tell me anything. Is it my fault? How can I fix that?  
  
```  
timedata                          :Set environmental variable 'DO_DIFF' to examine the difference.  
FAILED  
failure {{{  
Unexpected stdout  
}}}  
Expected STDOUT {{{  
\.\.\.found 4 targets\.\.\.  
\.\.\.updating 2 targets\.\.\.  
make bar  
time foo  
bar +user: [0-9\.]+ +system: +[0-9\.]+ +clock: +[0-9\.]+ *  
\.\.\.updated 2 targets\.\.\.$  
  
}}}  
Actual STDOUT {{{  
...found 4 targets...  
...updating 2 targets...  
make bar  
time foo  
bar user: 0.000000 system: 0.000000 clock: -990.515600  
...updated 2 targets...  
  
}}}  
changes caused by the last build command {{{  
Added files   : ['bar', 'foo']  
Removed files : []  
Modified files: []  
Touched files : []  
  
}}}  
stacktrace {{{  
at line 608 of D:\a\1\s\test\BoostBuild.py (fail_test)  
	from line 508 of D:\a\1\s\test\BoostBuild.py (run_build_system)  
	from line 89 of D:\a\1\s\test\timedata.py (basic_jam_action_test)  
	from line 176 of D:\a\1\s\test\timedata.py (<module>)  
	from line 62 of test_all.py (run_tests)  
	from line 346 of test_all.py (<module>)  
  
}}}  
```

---

## Comment 5

> Username: janisozaur  
> Created_at: 2019-11-14 21:48:40 UTC  
> Url: https://github.com/boostorg/build/pull/502#issuecomment-554095785  

Is the problem the negative value of the clock? `-` is not part of the matching expression. Perhaps re-run of the test would do?

---

## Comment 6

> Username: janisozaur  
> Created_at: 2019-11-14 22:19:35 UTC  
> Url: https://github.com/boostorg/build/pull/502#issuecomment-554106948  

Yay, all green now!

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2019-11-14 22:24:34 UTC  
> Url: https://github.com/boostorg/build/pull/502#issuecomment-554108978  

> Is the problem the negative value of the clock? `-` is not part of the matching expression. Perhaps re-run of the test would do?  
  
Yeah, the timing test suffers from a long standing bug that makes it fail inconsistently.

---
