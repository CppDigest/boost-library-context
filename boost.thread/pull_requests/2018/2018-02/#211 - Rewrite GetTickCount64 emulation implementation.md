# #211 Rewrite GetTickCount64 emulation implementation. [Merged]

> Username: Lastique  
> Created at: 2018-02-20 21:50:30 UTC  
> Updated at: 2018-03-10 23:14:46 UTC  
> Merged at: 2018-02-26 15:22:55 UTC  
> Closed at: 2018-02-26 15:22:55 UTC  
> Url: https://github.com/boostorg/thread/pull/211  

This is to resolve the possible license violation as the previous  
implementation has been taken from StackOverflow and was not licensed  
under the Boost Software License. The new implementation was adopted from  
Boost.Log:  
  
https://github.com/boostorg/log/blob/1cc577cbf5fae8f55c71c4493a7ef89027bd85dc/src/timestamp.cpp#L66-L86  
  
The legal issue has been raised in:  
  
https://lists.boost.org/Archives/boost/2018/02/241453.php  
  
Fixes https://github.com/boostorg/thread/issues/209.

---

## Comment 1

> Username: ned14  
> Created_at: 2018-02-21 14:45:31 UTC  
> Url: https://github.com/boostorg/thread/pull/211#issuecomment-367348603  

Unfortunately this PR would be a regression as currently written. The original bug report that I fixed was timers failing on a computer which sleeps for several months at a time. This PR does not detect and directly handle multiple 43 day system timer wraps as the original does.

---

## Comment 2

> Username: Lastique  
> Created_at: 2018-02-21 17:09:33 UTC  
> Url: https://github.com/boostorg/thread/pull/211#issuecomment-367398752  

I've updated the implementation to mitigate the GetTickCount wraparound problem if the user doesn't call GetTickCount64 for extended periods of time.

---

## Comment 3

> Username: ned14  
> Created_at: 2018-02-21 17:51:27 UTC  
> Url: https://github.com/boostorg/thread/pull/211#issuecomment-367411883  

Eh, surely a sleeping computer wouldn't call your refresh routine? Thus you get multiple wraps and the timer fires late?

---

## Comment 4

> Username: Lastique  
> Created_at: 2018-02-21 17:56:02 UTC  
> Url: https://github.com/boostorg/thread/pull/211#issuecomment-367413358  

Does `GetTickCount` keep going while the machine is sleeping?

---

## Comment 5

> Username: ned14  
> Created_at: 2018-02-21 18:10:49 UTC  
> Url: https://github.com/boostorg/thread/pull/211#issuecomment-367418074  

Yes

---

## Comment 6

> Username: Lastique  
> Created_at: 2018-02-21 18:21:17 UTC  
> Updated_at: 2018-02-21 18:26:35 UTC  
> Url: https://github.com/boostorg/thread/pull/211#issuecomment-367421187  

Oh well. Then if the machine sleeps for too long (more than ~49 days) the algorithm will miss one or more wraparounds. The current implementation doesn't seem to protect against that either, only it returns a bogus 0xFFFFFFFF value (not reliably - if the wrapped tick count doesn't match the condition in https://github.com/boostorg/thread/blob/develop/include/boost/thread/win32/thread_primitives.hpp#L124). Out of the two I'd prefer my result since it at least somehow correlates with reality.  
  
Anyways, let Vicente decide what to do with this PR. I'm fine if it's rejected.

---

## Comment 7

> Username: ned14  
> Created_at: 2018-02-21 19:48:05 UTC  
> Url: https://github.com/boostorg/thread/pull/211#issuecomment-367448318  

I honestly can't remember whether we specially handle the 32 bit GetTickCount64 emulation wrapping and returning -1. I do remember that we had a very eager bug reporter who did a lot of testing and verified the bug was fixed. Can't remember if that was on 64 bit only, and we threw WinXP to the wind.  
  
Actually, seeing as we've now dropped WinXP as a supported platform, we could just axe the emulation altogether? Vista and later implement GetLastCount64(), including on x86 platforms.

---

## Comment 8

> Username: Lastique  
> Created_at: 2018-02-21 20:05:04 UTC  
> Url: https://github.com/boostorg/thread/pull/211#issuecomment-367453586  

The special return value is 0xFFFFFFFF, not -1 (note that GetTickCount64 return ULONGLONG, the 64-bit unsigned integer). The only place that calls GetTickCount64 (https://github.com/boostorg/thread/blob/develop/include/boost/thread/detail/platform_time.hpp#L432) does not check for this value. And the genuine GetTickCount64 has no special return values to indicate errors, so checking for 0xFFFFFFFF would be a wrong thing to do anyway.  
  
> Actually, seeing as we've now dropped WinXP as a supported platform, we could just axe the emulation altogether?  
  
I'm not aware of Boost.Thread dropping support for XP. Boost in general certainly didn't drop XP.  
  
If Boost.Thread does not support pre-Vista then I agree, calling GetTickCount64 directly is more preferable.

---

## Comment 9

> Username: viboes  
> Created_at: 2018-02-22 06:56:15 UTC  
> Url: https://github.com/boostorg/thread/pull/211#issuecomment-367585454  

Thanks to both for working on this. Sorry, I know nothing about Windows.  
  
IIUC neither the current code nor the proposed patch are perfect solutions to the problem, isn't it?  
Could we state clearly what are the advantages/limitations of each approach?  
Niall, do you believe that Andrey solution is not satisfactory?  
  
IIUC, we use the Windows ::GetTickCount64 if available, isn't it?  
In the absence of 64 bits counters, do we really need an emulation? Couldn't we just have a restriction? It is too late?

---

## Comment 10

> Username: ned14  
> Created_at: 2018-02-22 08:54:45 UTC  
> Url: https://github.com/boostorg/thread/pull/211#issuecomment-367609739  

WinXP EOLed in 2014. WinVista EOLed last year, in 2017.  
  
If you feel that continuing to support WinXP is excessive, both of us agree that the GetTickCount64 emulation function ought to be removed entirely.  
  
So decide on that first, then we go from there.

---

## Comment 11

> Username: Lastique  
> Created_at: 2018-02-22 10:21:40 UTC  
> Url: https://github.com/boostorg/thread/pull/211#issuecomment-367635403  

> IIUC neither the current code nor the proposed patch are perfect solutions to the problem, isn't it?  
> Could we state clearly what are the advantages/limitations of each approach?  
  
Neither version works correctly if the machine sleeps for prolonged time. The PR version may miss one or more 32-bit wraparounds (which is ~49.7 days) in this case. E.g. if the machine sleeps for 50 days and then calls GetTickCount64, the result will change as if only ~0.3 days passed. The current git develop code may miss one or more 28-bit wraparounds (~3.1 days) or may return a bogus value of 0xFFFFFFFF regardless of the current time in this case.  
  
Aside from machine sleeps, the PR version should work correctly without restrictions (it will automatically refresh its internal state to track 32-bit wraparounds). The current develop code still requires the user to call the emulation code at least every ~6.2 days, otherwise it will either miss one or more 28-bit wraparounds or return 0xFFFFFFFF.

---

## Comment 12

> Username: Lastique  
> Created_at: 2018-02-22 10:26:31 UTC  
> Url: https://github.com/boostorg/thread/pull/211#issuecomment-367636865  

> IIUC, we use the Windows ::GetTickCount64 if available, isn't it?  
  
Yes, both versions do.

---

## Comment 13

> Username: viboes  
> Created_at: 2018-02-23 07:09:30 UTC  
> Url: https://github.com/boostorg/thread/pull/211#issuecomment-367928525  

Thanks to both.  
  
We need to support any version we support today, and in particular WinXP.  
Nial, my concern wasn't not about not supporting WinXp, but on not using an emulation for GetTickCount64, but this was surely a bad idea.   
  
I believe that the PR limitations are acceptable.   
Niall if you think the PR has any major issues, please tell us which.  
If no one is completely against, I'll merge this PR 25/02/2018.  
  
Vicente

---

## Comment 14

> Username: Kojoley  
> Created_at: 2018-03-10 22:37:58 UTC  
> Url: https://github.com/boostorg/thread/pull/211#issuecomment-372072704  

I am getting an error on Appveyor that I could not reproduce locally.  
```  
.\boost/thread/win32/thread_primitives.hpp(73): error C2143: syntax error: missing ')' before '*'  
.\boost/thread/win32/thread_primitives.hpp(73): error C2143: syntax error: missing ';' before '*'  
.\boost/thread/win32/thread_primitives.hpp(73): error C2059: syntax error: ')'  
.\boost/thread/win32/thread_primitives.hpp(73): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
.\boost/thread/win32/thread_primitives.hpp(74): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
.\boost/thread/win32/thread_primitives.hpp(74): error C2365: 'boost::detail::win32::detail::gettickcount64_t': redefinition; previous definition was 'function'  
.\boost/thread/win32/thread_primitives.hpp(73): note: see declaration of 'boost::detail::win32::detail::gettickcount64_t'  
.\boost/thread/win32/thread_primitives.hpp(74): error C2146: syntax error: missing ';' before identifier 'gettickcount64'  
```  
  
Update: It looks like Boost.Thread CI is experiencing the same issue.

---

## Comment 15

> Username: Lastique  
> Created_at: 2018-03-10 23:14:46 UTC  
> Url: https://github.com/boostorg/thread/pull/211#issuecomment-372075016  

See https://github.com/boostorg/thread/pull/214.

---
