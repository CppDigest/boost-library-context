# #1070 - Please provide Windows ARM64  binaries. [Open]

> Username: vask2108  
> Created at: 2025-08-04 15:51:04 UTC  
> Updated at: 2025-10-29 19:35:10 UTC  
> Url: https://github.com/boostorg/boost/issues/1070  

Hi,  
Recently Windows On Arm64 CI GHA ( (windows-11-arm)) runner are added for April 2025.   
  
Can you please provide official Boost API binaries for Windows on Arm64 (WoA) platform ? Is that something feasible ?

---

## Comment 1

> Username: vask2108  
> Created at: 2025-08-28 19:28:23 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3234693701  

Hello maintainers,  
I have added PR to Add  Windows on ARM64 GitHub Actions CI support . Can you please review and see if that helps to make a formal release for Windows on the ARM64 platform also?  
  
https://github.com/boostorg/boost/pull/1077/  
  
Thank you.

---

## Comment 2

> Username: Akash9824  
> Created at: 2025-09-21 10:15:24 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3315897180  

Thank you @vask2108  for your efforts in porting this library.  
  
Windows on ARM is gaining traction, and this library is quite important. Getting an ARM64 version is essential, and I hope it will soon get @olk 's attention and that the PR will be reviewed.

---

## Comment 3

> Username: vask2108  
> Created at: 2025-09-22 08:15:20 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3317500888  

@Akash9824 . Thank you. Yes Windows on ARM64 is gaining traction and boost APIs are quite important. Waiting for PR to be reviewed by maintainers.

---

## Comment 4

> Username: vask2108  
> Created at: 2025-09-23 16:08:35 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3324671013  

@pdimov   
  
Hope you are doing well.   
  
Any information, how we can get Boost API binaries formally released for Windows ARM64 platform? There is great interest  for Windows ARM64 .  
  
Thank you.

---

## Comment 5

> Username: pdimov  
> Created at: 2025-09-23 16:55:41 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3324841726  

We don't formally release binaries; the official releases are source only. (Although I now see that while the old site made that clear at https://original.boost.org/users/download/, the new one lists the Windows releases at https://www.boost.org/releases/latest/ without marking them as not-quite-official in any way.)  
  
We do have semi-official binaries for Windows, maintained by Tom Kent (@teeks99). I don't know whether he'd be willing to maintain Windows ARM ones.  
  
If you or anyone else volunteers to provide Windows ARM binaries, we'll link to them on the download page.

---

## Comment 6

> Username: Akash9824  
> Created at: 2025-09-26 07:40:37 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3337178354  

@vask2108 can we provide compile binaries to them?

---

## Comment 7

> Username: vask2108  
> Created at: 2025-09-26 15:12:25 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3339112974  

@teeks99  
Are you willing to support Windows ARM64 binaries as well in your semi official releases? It will be great help to community.   
  
https://github.com/boostorg/boost/issues/1070#issuecomment-3324841726  
  
Thank you.

---

## Comment 8

> Username: teeks99  
> Created at: 2025-09-26 16:07:07 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3339381323  

I'm open to it.  
  
I don't think it would be hard to add a cross-compile into my current setup. However, I don't have anywhere that I can test them, so I'm hesitant to add them to something that gets released.

---

## Comment 9

> Username: Akash9824  
> Created at: 2025-09-29 05:51:24 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3345088407  

> I'm open to it.  
>   
> I don't think it would be hard to add a cross-compile into my current setup. However, I don't have anywhere that I can test them, so I'm hesitant to add them to something that gets released.  
  
Do you mean you don't have a WOA setup to run benchmark tests?

---

## Comment 10

> Username: vask2108  
> Created at: 2025-09-29 07:21:16 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3345408845  

@teeks99   
Thank  you for the response.  
What are your test requirements? I can assess and tell, if I can help.  
Now Window ARM64 also have GHA runners, which can be used to test?

---

## Comment 11

> Username: teeks99  
> Created at: 2025-10-01 10:36:09 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3355723933  

I'll have to figure out the test requirements. Right now the whole build (and test) process for windows runs on a single (32-core) VM, and still takes a couple hours to complete. Somehow this will need to be split to include an ARM64 machine.  
  
Unfortunately, the GHA runners are not sufficient for the general build process. I'll have to play around and see if they'll work for the ARM64 case, but I suspect they are underpowered.

---

## Comment 12

> Username: vask2108  
> Created at: 2025-10-01 22:52:40 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3358430111  

@teeks99   
Thank you. Please try, let us hope that works  :-)

---

## Comment 13

> Username: vask2108  
> Created at: 2025-10-07 19:06:01 UTC  
> Updated at: 2025-10-07 19:06:54 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3378331912  

@teeks99   
Hope you are doing well.  sorry to bother you , had a chance to check this?   
Thank you.

---

## Comment 14

> Username: vask2108  
> Created at: 2025-10-14 20:48:03 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3403566880  

@teeks99   
  
Hope you are doing well.  
  
If you think having extra hardware is going to unblock you, please let me know. I can try whether required hardware can be provided to you. I am not promising at the moment.  But I can try, If I know what you actually need to provide Windows ARM64 binaries as part of your releases.  
  
Thank you.

---

## Comment 15

> Username: teeks99  
> Created at: 2025-10-16 00:30:47 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3408727551  

I haven't had a chance to dig into this and probably won't for a bit. It will require a substantial re-working of how I do things, I think I have a decent plan, but it will take a fair bit of effort. Since we're just a few weeks from the next beta, I probably won't have it for this next release. Hopefully I can find some time to work it in there though, I'd really hope to have it before the 1.91 release.

---

## Comment 16

> Username: vask2108  
> Created at: 2025-10-20 12:52:53 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3421937428  

@teeks99   
Thank you.

---

## Comment 17

> Username: vask2108  
> Created at: 2025-10-29 19:35:10 UTC  
> Url: https://github.com/boostorg/boost/issues/1070#issuecomment-3463514836  

@teeks99  If you need any further support for  Windows ARM. please check @anthony-linaro. He may be able to help.  
Thank you.
