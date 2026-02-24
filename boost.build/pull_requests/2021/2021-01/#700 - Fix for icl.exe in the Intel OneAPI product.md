# #700 Fix for icl.exe in the Intel OneAPI product [Merged]

> Username: eldiener  
> Created at: 2021-01-08 15:20:27 UTC  
> Updated at: 2021-10-02 20:59:46 UTC  
> Merged at: 2021-01-18 16:40:47 UTC  
> Closed at: 2021-01-18 16:40:47 UTC  
> Url: https://github.com/boostorg/build/pull/700  

The icl.exe major version number in the oneAPI product starts with "2021" rather than "21", but the fix supports also specifying the major version number of icl.exe in the oneAPI product as starting with "21". In the oneAPI product the setup file is called "setvars.bat" and not "iclvars.bat". Finally all tests for icl major versions are changed from the previously faulty string comparisons to the correct number comparisons.

---

## Comment 1

> Username: eldiener  
> Created_at: 2021-01-17 21:44:26 UTC  
> Url: https://github.com/boostorg/build/pull/700#issuecomment-761885365  

Please merge.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2021-01-18 16:39:50 UTC  
> Url: https://github.com/boostorg/build/pull/700#issuecomment-762359825  

Sorry for the delay.. was occupied adding oneAPI support for build.sh, and the fallout from that.

---

## Comment 3

> Username: UpasakhaJason  
> Created_at: 2021-02-07 16:59:19 UTC  
> Url: https://github.com/boostorg/build/pull/700#issuecomment-774708837  

On Windows, the OneAPI installation has a different directory structure than the classic Parallel Studio installation.  It looks like intel-win.jam is still assuming the same directory structure for icl version 2021.1.  Is this something that will be addressed?

---

## Comment 4

> Username: eldiener  
> Created_at: 2021-02-07 20:45:16 UTC  
> Url: https://github.com/boostorg/build/pull/700#issuecomment-774757482  

It should be addresses in this fix. Please take a look at how it is determined where the icl.exe is compared to the root directory.

---

## Comment 5

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:45 UTC  
> Url: https://github.com/boostorg/build/pull/700#issuecomment-932819694  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
