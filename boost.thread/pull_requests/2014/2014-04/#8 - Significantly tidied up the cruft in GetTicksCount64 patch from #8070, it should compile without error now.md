# #8 Significantly tidied up the cruft in GetTicksCount64 patch from #8070, it should compile without error now. [Closed]

> Username: ned14  
> Created at: 2014-04-16 11:26:07 UTC  
> Updated at: 2014-07-05 18:42:09 UTC  
> Closed at: 2014-07-05 18:42:09 UTC  
> Url: https://github.com/boostorg/thread/pull/8  



---

## Comment 1

> Username: viboes  
> Created_at: 2014-04-16 16:50:51 UTC  
> Url: https://github.com/boostorg/thread/pull/8#issuecomment-40623159  

I need to be sure that your patch works on develop branch before pulling it to master branch.  
Do you mind to provide first a patch for develop branch?

---

## Comment 2

> Username: ned14  
> Created_at: 2014-04-16 17:02:44 UTC  
> Url: https://github.com/boostorg/thread/pull/8#issuecomment-40624521  

I'll look into it tomorrow morning if you like, but to be honest it ought to be clean. The replacement code is like ten lines long, replacing something like 100 lines all of which I deleted.

---

## Comment 3

> Username: viboes  
> Created_at: 2014-04-16 17:55:44 UTC  
> Url: https://github.com/boostorg/thread/pull/8#issuecomment-40630765  

I don't master git. Please, could you tell me how can I merge your push into develop?

---

## Comment 4

> Username: ned14  
> Created_at: 2014-04-16 23:43:14 UTC  
> Url: https://github.com/boostorg/thread/pull/8#issuecomment-40665823  

If you'd like a pull request as a patchset, simply add .patch to the end of this page's URL. Like this:  
  
https://github.com/boostorg/thread/pull/8.patch  
  
I really wish github had obvious buttons for stuff like that :(  
  
Niall

---

## Comment 5

> Username: ned14  
> Created_at: 2014-04-17 10:31:30 UTC  
> Url: https://github.com/boostorg/thread/pull/8#issuecomment-40701040  

Bad news Vicente: my develop branch in my fork of boost.thread is not your develop branch in boostorg, so for some reason the github fork process has borked the develop branch.  
  
I therefore cannot send you a pull request straight to develop branch. I can tell you though that the patch from https://github.com/boostorg/thread/pull/8.patch applies cleanly to your develop branch, and you can apply that patch to any branch you like using 'git apply'.  
  
If you'd like to give me write access to boostorg.thread, I could break the patch into its own branch off of your develop branch and issue the pull request that way? If you're smiling on my permit object contribution, it'll probably be easier long run for me to fix reported bugs in it anyway.

---

## Comment 6

> Username: ned14  
> Created_at: 2014-04-23 17:02:18 UTC  
> Url: https://github.com/boostorg/thread/pull/8#issuecomment-41187141  

I've been thinking about this patch, and I don't like how the 64 bit ticks emulation wraps to 0xffffffff00000000 after 40 days or whatever it is - it will break code. How does a GetPerformanceCounter() based emulation sound?

---

## Comment 7

> Username: viboes  
> Created_at: 2014-04-23 18:04:43 UTC  
> Url: https://github.com/boostorg/thread/pull/8#issuecomment-41194416  

Are you telling that your patch is not good?

---

## Comment 8

> Username: ned14  
> Created_at: 2014-04-23 19:03:15 UTC  
> Url: https://github.com/boostorg/thread/pull/8#issuecomment-41201226  

The GetTicksCount64 emulation when the kernel doesn't provide it is no good, no. It only occurred to me over the weekend. However, it's an easy fix, shall I fix it?

---

## Comment 9

> Username: ned14  
> Created_at: 2014-04-23 19:37:39 UTC  
> Url: https://github.com/boostorg/thread/pull/8#issuecomment-41204887  

Hmm, the QueryPerformanceCounter solution causes boost::chrono::steady_clock::now() to assert fail, which is really weird. I'll get back to you soon.

---

## Comment 10

> Username: viboes  
> Created_at: 2014-04-23 19:49:51 UTC  
> Url: https://github.com/boostorg/thread/pull/8#issuecomment-41206207  

I have some trouble with the patch. It has 41 files. What is wrong?

---

## Comment 11

> Username: ned14  
> Created_at: 2014-04-23 20:14:52 UTC  
> Url: https://github.com/boostorg/thread/pull/8#issuecomment-41208982  

Looks like I accidentally merged the permit object. I'll back it out shortly. BTW found a GetTickCount64() emulation at https://stackoverflow.com/questions/8211820/userland-interrupt-timer-access-such-as-via-kequeryinterrupttime-or-similar, testing it now.

---

## Comment 12

> Username: ned14  
> Created_at: 2014-04-23 20:22:17 UTC  
> Url: https://github.com/boostorg/thread/pull/8#issuecomment-41209810  

Ok, fix made, all unit tests pass.

---

## Comment 13

> Username: ned14  
> Created_at: 2014-07-05 18:42:09 UTC  
> Url: https://github.com/boostorg/thread/pull/8#issuecomment-48093470  

Replaced with pull #20

---
