# #213 - intellisense doesn't seem to work when the beast header is included. [Closed]

> Username: taenaism  
> Created at: 2016-12-23 15:42:30 UTC  
> Updated at: 2017-01-10 23:48:15 UTC  
> Closed at: 2017-01-10 23:48:15 UTC  
> Url: https://github.com/boostorg/beast/issues/213  

If I include this header, intellisense seem to stop working.  
  
#include <beast\http.hpp>  
  
I am using Visual Studio 2015 Update 3. I tried it with a new project and just copy and paste the example code in and also added the necessary includes. What could be the problem?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-12-23 16:42:29 UTC  
> Url: https://github.com/boostorg/beast/issues/213#issuecomment-269015787  

No idea. Try using a forward slash?

---

## Comment 2

> Username: maddinat0r  
> Created at: 2016-12-24 10:05:31 UTC  
> Url: https://github.com/boostorg/beast/issues/213#issuecomment-269077985  

That's an IntelliSense bug. Already reported it [here](https://connect.microsoft.com/VisualStudio/feedback/details/3114172/intellisense-stops-working-after-using-a-specific-template-function), didn't get much reaction though.  
If you remove [these lines](https://github.com/vinniefalco/Beast/blob/09c183d1124385ab5dcd9a2e5c4df819bd94807c/include/beast/core/detail/is_call_possible.hpp#L64-L83), IntelliSense should start working again.

---

## Comment 3

> Username: taenaism  
> Created at: 2016-12-24 11:44:35 UTC  
> Url: https://github.com/boostorg/beast/issues/213#issuecomment-269081149  

Thanks for your reply!  
  
I will give this a try. This is a wonderful library. :)  
  
On 24 Dec 2016, at 6:05 PM, Alex Martin <notifications@github.com<mailto:notifications@github.com>> wrote:  
  
  
That's an IntelliSense bug. Already reported it here<https://connect.microsoft.com/VisualStudio/feedback/details/3114172/intellisense-stops-working-after-using-a-specific-template-function>, didn't get much reaction though.  
If you remove these lines<https://github.com/vinniefalco/Beast/blob/09c183d1124385ab5dcd9a2e5c4df819bd94807c/include/beast/core/detail/is_call_possible.hpp#L64-L83>, IntelliSense should start working again.  
  
-  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/vinniefalco/Beast/issues/213#issuecomment-269077985>, or mute the thread<https://github.com/notifications/unsubscribe-auth/ATp9C1qkAStmkP113CKY31G3N1pfcT7Oks5rLO5sgaJpZM4LU5yO>.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-12-24 12:57:14 UTC  
> Url: https://github.com/boostorg/beast/issues/213#issuecomment-269083424  

This is great, thanks!!!

---

## Comment 5

> Username: merryhime  
> Created at: 2016-12-25 09:59:06 UTC  
> Updated at: 2016-12-25 09:59:38 UTC  
> Url: https://github.com/boostorg/beast/issues/213#issuecomment-269116536  

You can [wrap the lines with an `#ifndef __INTELLISENSE__` guard](https://blogs.msdn.microsoft.com/vcblog/2011/03/29/troubleshooting-tips-for-intellisense-slowness/) to disable parsing during an intellisense pass.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2016-12-25 13:36:13 UTC  
> Url: https://github.com/boostorg/beast/issues/213#issuecomment-269123063  

Thank you that's perfect!
