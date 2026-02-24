# #51 - Recent changes to is_running failing on Solaris [Closed]

> Username: jake-at-work  
> Created at: 2018-09-26 23:18:00 UTC  
> Updated at: 2018-09-27 17:04:08 UTC  
> Closed at: 2018-09-27 17:04:08 UTC  
> Url: https://github.com/boostorg/process/issues/51  

With the recent addition of the `WIFSIGNALED` macro use the `is_running` function is consistently returning false do to differences in the behavior of this macro on Solaris.   
  
First off the static assert fails. The code `0177` results in `WIFSIGNALED` returning `true` rather than `false`. I commented that out to see if things would run.  
  
This is where it gets strange. After the successful execution a child the status code is `0177`, which when checked in `is_running` results in a `WIFSIGNALED` being true and the function returning false. A check of the child's exit code, using `WTERMSIG` since `WIFSIGNALED` is true, results in the value `0177` (127), which appears to not be a valid signal code on Solaris.  
  
Looking at *wait.h* on Solaris it defines these macros quite differently than say MacOS.   
On Solaris:  
```  
#define WIFEXITED(stat)         ((int)((stat)&0xFF) == 0)  
#define WIFSIGNALED(stat)       ((int)((stat)&0xFF) > 0 && \  
                                    (int)((stat)&0xFF00) == 0)  
#define WIFSTOPPED(stat)        ((int)((stat)&0xFF) == 0177 && \  
                                    (int)((stat)&0xFF00) != 0)  
#define WEXITSTATUS(stat)       ((int)(((stat)>>8)&0xFF))  
#define WTERMSIG(stat)          ((int)((stat)&0x7F))  
#define WSTOPSIG(stat)          ((int)(((stat)>>8)&0xFF))  
```  
From this we can clearly see why we get the values we do on Solaris when code is `0177`, since `(int)((stat)&0xFF) > 0` is true and so is `(int)((stat)&0xFF00) == 0`.  
  
On MacOS:  
```  
#define _WSTATUS(x)     (_W_INT(x) & 0177)  
#define _WSTOPPED       0177   
#define WSTOPSIG(x)     (_W_INT(x) >> 8)  
#define WIFCONTINUED(x) (_WSTATUS(x) == _WSTOPPED && WSTOPSIG(x) == 0x13)  
#define WIFSTOPPED(x)   (_WSTATUS(x) == _WSTOPPED && WSTOPSIG(x) != 0x13)  
#define WIFEXITED(x)    (_WSTATUS(x) == 0)  
#define WIFSIGNALED(x)  (_WSTATUS(x) != _WSTOPPED && _WSTATUS(x) != 0)  
#define WTERMSIG(x)     (_WSTATUS(x))  
```  
Clearly we can see that the code `0177` case is handled in the `WIFSIGNALED` and would result in the expected `false`.  
  
It almost feels like the Solaris implementation is missing the *stopped* case in it's &*signaled* check.  
  
Not being an expert in this particular area I hesitate to make any changes other then to revert back to prior implementations that only used the `WIFEXITED` macro but would like to have an answer for how to upgrade in the future.  
  
Any thoughts on how to proceed?

---

## Comment 1

> Username: jake-at-work  
> Created at: 2018-09-26 23:35:06 UTC  
> Url: https://github.com/boostorg/process/issues/51#issuecomment-424904526  

Interestingly if I redefine the Solaris macros to match the MacOS macros all is seemingly well. I feel like maybe there is a combination using `WIFSTOPPED` that might address this issue. I might explore that a little.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2018-09-27 03:23:41 UTC  
> Url: https://github.com/boostorg/process/issues/51#issuecomment-424946236  

So `WIFSIGNALED` ought to be handled, because it means the child was terminated by signal.  
  
There are basically two solutions: either check for the OS with an `#if defined` and change the value or `still_running` or add the `WIFSIGNALED` only if it works with `0177`. The value `0177` is reversed engineered from linux anyhow, so I'd be for the first solution.   
  
I can't develop on Solaris and would greatly appreciate it if you find a solution or even create a PR.   
  
Thanks for the bug report.

---

## Comment 3

> Username: jake-at-work  
> Created at: 2018-09-27 04:35:42 UTC  
> Url: https://github.com/boostorg/process/issues/51#issuecomment-424956306  

Ok, I will whip up a fix. I think I found it actually. I incorrectly assumed that the status `0177` was coming from the `is_running` method that calls `pidwait` but it turns out that this method has not been invoked yet. What is invoking is `child_decl::_exited()`, which calls `is_running` with the initial value in the `_exit_status` atomic. It is initialized to `0177` which is apparently Solaris for *signaled* with signal number 127. Doh!  
  
So I think the easy fix may be adjusting that initial value depending on the OS. I will take a crack at it laster this week.

---

## Comment 4

> Username: jake-at-work  
> Created at: 2018-09-27 05:03:42 UTC  
> Url: https://github.com/boostorg/process/issues/51#issuecomment-424960037  

On Solaris value `0177777` indicates it the process was continued. This seems like a reasonable initial value. It passes the `static_assert` as it is both not `WIFEXITED` nor `WIFSIGNALED`.  I will see if this value or similar is consistent across more systems, otherwise `#ifdef` time.
