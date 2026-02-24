# #14 [winrt support] Adding a new feature to help support building for the Windows Runtime [Closed]

> Username: stgates  
> Created at: 2014-05-28 00:27:41 UTC  
> Updated at: 2021-10-02 22:35:55 UTC  
> Closed at: 2014-07-11 07:52:14 UTC  
> Url: https://github.com/boostorg/build/pull/14  

Changes to enable building for the Windows Runtime (store/phone). Separated out from previous pull request, https://github.com/boostorg/build/pull/9. The changes for running tests for the Windows Runtime from the previous pull request will come in a separate change once this one is done.  
1. Adds a new feature <windows-api> for specifying the WINAPI_FAMILY API set to target. Defaults to desktop, which is how builds before my changes.  
2. A few small changes for adding ARM support.  
3. Changes to call to correct vcvars setup script for Windows Phone 8.

---

## Comment 1

> Username: stgates  
> Created_at: 2014-06-14 17:38:33 UTC  
> Url: https://github.com/boostorg/build/pull/14#issuecomment-46094404  

Volodya, have you had a chance to look at these changes based on your feedback? Both this and pull request #13 I believe address your comments from my previous request #9.   
  
Thanks,  
Steve

---

## Comment 2

> Username: stgates  
> Created_at: 2014-07-02 21:44:07 UTC  
> Url: https://github.com/boostorg/build/pull/14#issuecomment-47840999  

Has anyone had a chance to look at this? I initially submitted this request in in #9 back in April and pulled it out in May to make a smaller easier to review piece.  
  
This is important for being able to easily build targeting the Windows Runtime. Let me know if there is anything that needs to be done.  
  
Thanks,  
Steve

---

## Comment 3

> Username: vprus  
> Created_at: 2014-07-02 21:47:38 UTC  
> Url: https://github.com/boostorg/build/pull/14#issuecomment-47841365  

Sorry for delay, I've missed this one. I'll take a look next week.

---

## Comment 4

> Username: stgates  
> Created_at: 2014-07-03 16:34:13 UTC  
> Url: https://github.com/boostorg/build/pull/14#issuecomment-47953339  

Thanks much appreciated, this contribution is important for making it easy for others to build for Windows Store and Windows Phone.

---

## Comment 5

> Username: vprus  
> Created_at: 2014-07-09 06:04:14 UTC  
> Url: https://github.com/boostorg/build/pull/14#issuecomment-48433124  

Hi,  
  
I went through the patches adding various line comments. It would be nice to have those addressed, but overall the patch looks good.

---

## Comment 6

> Username: stgates  
> Created_at: 2014-07-09 06:07:29 UTC  
> Url: https://github.com/boostorg/build/pull/14#issuecomment-48433292  

Absolutely I’ll go through and take a look at them all tomorrow. Thanks again.  
  
From: Vladimir Prus [mailto:notifications@github.com]  
Sent: Tuesday, July 8, 2014 11:04 PM  
To: boostorg/build  
Cc: Steve Gates  
Subject: Re: [build] [winrt support] Adding a new feature to help support building for the Windows Runtime (#14)  
  
Hi,  
  
I went through the patches adding various line comments. It would be nice to have those addressed, but overall the patch looks good.  
  
—  
Reply to this email directly or view it on GitHubhttps://github.com/boostorg/build/pull/14#issuecomment-48433124.

---

## Comment 7

> Username: stgates  
> Created_at: 2014-07-11 01:22:17 UTC  
> Url: https://github.com/boostorg/build/pull/14#issuecomment-48684777  

Ok I think I've addressed all your feedback. Let me know what you think and when this might have a chance of being merged into the develop branch.  
  
Thanks,  
Steve

---

## Comment 8

> Username: vprus  
> Created_at: 2014-07-11 07:52:14 UTC  
> Url: https://github.com/boostorg/build/pull/14#issuecomment-48703520  

I've merged your changes and pushed to develop. Please give a test in case something went wrong at the last minute - I don't have setup to actually test this.  
  
Thanks much for your contribution!

---

## Comment 9

> Username: stgates  
> Created_at: 2014-07-11 08:39:47 UTC  
> Url: https://github.com/boostorg/build/pull/14#issuecomment-48707168  

Hi Vladimir,  
  
I discovered a list minute issue that happens if the Windows Phone setup scripts are not present with the Visual Studio installation. This was my mistake I should have thought to have tested for this scenario. The fix is straightforward I have a pull request https://github.com/boostorg/build/pull/18 that addresses the problem.  
  
Thanks,  
Steve

---
