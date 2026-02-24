# #67 Do not wake up computer from sleep [Closed]

> Username: mauve  
> Created at: 2015-09-23 12:55:09 UTC  
> Updated at: 2015-09-29 20:48:26 UTC  
> Closed at: 2015-09-27 12:31:42 UTC  
> Url: https://github.com/boostorg/thread/pull/67  

Supplying a REASON_CONTEXT to SetWaitableTimerEx() will wakeup  
the computer from sleep. Supplying a nullptr instead disables wake.  
  
Fixes: https://svn.boost.org/trac/boost/ticket/11368

---

## Comment 1

> Username: viboes  
> Created_at: 2015-09-23 17:26:14 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-142671502  

Thanks for the patch. I'm not a Windows expert. We will merge it as soon as Niall has time to check it.

---

## Comment 2

> Username: ned14  
> Created_at: 2015-09-23 18:28:13 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-142689493  

This is not a documented valid value for the WakeContext parameter. I would be happy to sign off on this anyway, however neither Wine nor ReactOS fully implement SetWaitableTimerEx() so I cannot tell what the valid values are from them either. The NT kernel docs are similarly not helpful: https://msdn.microsoft.com/en-us/library/windows/hardware/ff553249(v=vs.85).aspx.  
  
Vicente you could try merging this patch and see what happens? As I mentioned, I never saw a problem on my machine with the current code anyway, so it is hard for me to say if this patch is good or bad.

---

## Comment 3

> Username: MarcelRaad  
> Created_at: 2015-09-23 18:29:25 UTC  
> Updated_at: 2015-09-23 18:30:19 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-142689788  

We're successfully using principally the same patch. But the nullptr should probably be changed to 0 or NULL in order to support old compilers?

---

## Comment 4

> Username: mauve  
> Created_at: 2015-09-23 19:21:22 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-142702875  

we looked at the disassembled source for SetWaitableTimerEx, as it turns out, setting wakecontext to null actually calls SetWaitableTimer :D

---

## Comment 5

> Username: mauve  
> Created_at: 2015-09-23 19:21:45 UTC  
> Updated_at: 2015-09-23 19:29:38 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-142702948  

@MarcelRaad should I switch to NULL instead?  
  
the rest of the file used NULL so I switched and force-pushed

---

## Comment 6

> Username: ned14  
> Created_at: 2015-09-23 21:11:34 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-142729927  

> we looked at the disassembled source for SetWaitableTimerEx, as it turns out, setting wakecontext to null actually calls SetWaitableTimer :D  
  
This would suggest that we revert my patch to its previous edition.  
  
It might be worth asking on stackoverflow how to correctly call SetWaitableTimerEx.  
  
Niall

---

## Comment 7

> Username: ned14  
> Created_at: 2015-09-23 21:30:08 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-142735517  

Question asked to https://stackoverflow.com/questions/32749829/how-to-call-setwaitabletimerex-correctly

---

## Comment 8

> Username: ned14  
> Created_at: 2015-09-25 00:13:57 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-143086453  

I'm thinking that one could pass a NULL WakeContext when wait time exceeds some amount? That would disable PC wake, but keep timer coalescing for shorter values. What's a reasonable amount? Ten seconds?

---

## Comment 9

> Username: mauve  
> Created_at: 2015-09-25 06:25:33 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-143140546  

@ned14: I was thinking about that as well. But on the other hand are coalescing timers really useful without wake?

---

## Comment 10

> Username: slikts  
> Created_at: 2015-09-27 01:44:21 UTC  
> Updated_at: 2015-09-27 01:44:30 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-143512008  

I'll just [leave this here](https://community.spotify.com/t5/Help-Desktop-Linux-Windows-Web/Windows-Spotify-wakes-computer-from-sleep/m-p/1212180#M140525).

---

## Comment 11

> Username: viboes  
> Created_at: 2015-09-27 06:19:30 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-143523183  

I can not take this PR as it is for master. I'll propagate this PR to develop and see how it behaves.   
  
Niall, please, come back to this issue once you have more time.

---

## Comment 12

> Username: viboes  
> Created_at: 2015-09-27 12:31:42 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-143546747  

Patch applied to develop branch  
  
https://github.com/boostorg/thread/commit/9ac736a4c4214cdb01caaa84a1121b3846dc87b4

---

## Comment 13

> Username: mauve  
> Created_at: 2015-09-29 19:51:07 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-144169487  

@ned14: do you want me to try and revert the following commit or should I just prepare a commit which removes the parts about SetWaitableTimerEx and switch back to SetWaitableTimer? Personally I am unsure if coalescing timers are useful without wakeup (no need to coalesce if already running the CPU at full speed).  
  
```  
commit 401f69f108271fe2531832a3d85921f019aca421  
Author: Niall Douglas (s [underscore] sourceforge {at} nedprod [dot] com) <spam@nowhere>  
Date:   Wed Feb 4 13:58:11 2015 +0000  
  
    Added coalesced timer support to Windows where that API is available. Tolerable delay is set to the maximum of 5% of interval or 32 ms.  
```

---

## Comment 14

> Username: viboes  
> Created_at: 2015-09-29 20:23:18 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-144181123  

Hi Mauve,  
  
I will be very grateful if you go ahead and provide a PR to revert this commit.  
  
Vicente

---

## Comment 15

> Username: mauve  
> Created_at: 2015-09-29 20:40:45 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-144185236  

@viboes: Do you mind if I wait with the revert for this PR and do that at the same time I prepare a "proper" fix according to what @ned14 wants me to do? Despite not being beautiful this PR fixes a serious issue.

---

## Comment 16

> Username: ned14  
> Created_at: 2015-09-29 20:43:49 UTC  
> Updated_at: 2015-09-29 20:44:15 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-144185957  

Stackoverflow says the current patch is the right one https://stackoverflow.com/questions/32749829/how-to-call-setwaitabletimerex-correctly

---

## Comment 17

> Username: mauve  
> Created_at: 2015-09-29 20:48:26 UTC  
> Url: https://github.com/boostorg/thread/pull/67#issuecomment-144187180  

@ned14: Perfect thanks, I will prepare a patch to cleanup the remaining unused wakecontext code.

---
