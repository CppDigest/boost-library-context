# #188 Cleanup [Merged]

> Username: austin-beer  
> Created at: 2017-10-26 14:15:20 UTC  
> Updated at: 2017-10-27 17:39:41 UTC  
> Merged at: 2017-10-27 06:10:06 UTC  
> Closed at: 2017-10-27 06:10:06 UTC  
> Url: https://github.com/boostorg/thread/pull/188  

These are a series of commits that been sitting in my local workspace for a couple of weeks now. I was going to wait until after I'd finished the win32 changes before submitting them, but I decided to go ahead and submit them now to get them out of the way.  
  
See the commit log for the list of changes I made.

---

## Comment 1

> Username: viboes  
> Created_at: 2017-10-26 18:49:12 UTC  
> Url: https://github.com/boostorg/thread/pull/188#issuecomment-339762747  

Great we start to see green run in windows :)

---

## Comment 2

> Username: austin-beer  
> Created_at: 2017-10-26 18:55:56 UTC  
> Url: https://github.com/boostorg/thread/pull/188#issuecomment-339764656  

A few green runs, at least. :)

---

## Comment 3

> Username: austin-beer  
> Created_at: 2017-10-26 23:28:22 UTC  
> Url: https://github.com/boostorg/thread/pull/188#issuecomment-339829423  

FYI, the 64-bit Windows builds are failing due the unit tests exceeding the 260 character filepath limit on Windows. Every filepath includes `\msvc-14.1\debug\address-model-64\threadapi-win32\threading-multi`. Do you know if there is an easy way to shorten this? On the 32-bit builds the filepaths don't have `\address-model-64` which is just enough to keep them under the 260 character limit so they don't fail.

---

## Comment 4

> Username: viboes  
> Created_at: 2017-10-27 05:18:07 UTC  
> Updated_at: 2017-10-27 05:18:58 UTC  
> Url: https://github.com/boostorg/thread/pull/188#issuecomment-339875152  

Thanks for the info. `/threadapi-win32` was added in order to do cross compiling.  
  
No I don't know how to shorten them.   
  
Could you create a issue for Boost.Build with the specific compiler report?

---

## Comment 5

> Username: viboes  
> Created_at: 2017-10-27 06:02:11 UTC  
> Url: https://github.com/boostorg/thread/pull/188#issuecomment-339880777  

Steven Watanabe said on Boost ML  
"  
--abbreviate-paths  
  
http://www.boost.org/build/doc/html/bbv2/reference/buildprocess.html#bbv2.reference.buildprocess.targetpath  
"

---

## Comment 6

> Username: viboes  
> Created_at: 2017-10-27 06:10:00 UTC  
> Url: https://github.com/boostorg/thread/pull/188#issuecomment-339881862  

I've added --abbreviate-paths

---

## Comment 7

> Username: austin-beer  
> Created_at: 2017-10-27 15:15:17 UTC  
> Url: https://github.com/boostorg/thread/pull/188#issuecomment-340000388  

--abbreviate-paths sounds like the solution we need. Thanks for adding that.

---

## Comment 8

> Username: austin-beer  
> Created_at: 2017-10-27 17:06:42 UTC  
> Url: https://github.com/boostorg/thread/pull/188#issuecomment-340029131  

Glad to help. Which AppVeyor build are you looking at that's all green?

---
