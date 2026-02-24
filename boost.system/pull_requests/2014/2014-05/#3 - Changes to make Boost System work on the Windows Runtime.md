# #3 [winrt support] Changes to make Boost System work on the Windows Runtime. [Merged]

> Username: stgates  
> Created at: 2014-05-27 19:35:23 UTC  
> Updated at: 2014-06-17 18:51:41 UTC  
> Merged at: 2014-06-04 15:06:40 UTC  
> Closed at: 2014-06-04 15:06:40 UTC  
> Url: https://github.com/boostorg/system/pull/3  

Basically just use FormatMessageW without FORMAT_MESSAGE_FROM_SYSTEM. Determining whether the Windows Runtime is currently being targeted is done with a Boost Predef.

---

## Comment 1

> Username: stgates  
> Created_at: 2014-05-27 21:17:09 UTC  
> Url: https://github.com/boostorg/system/pull/3#issuecomment-44336647  

Please note this change depends on an outstanding pull request I have on the Config library to define BOOST_NO_ANSI_APIS.  
  
https://github.com/boostorg/config/pull/16

---

## Comment 2

> Username: Beman  
> Created_at: 2014-05-27 21:33:22 UTC  
> Url: https://github.com/boostorg/system/pull/3#issuecomment-44338444  

Where is the #include <boost/predef/platform.h>? Or am I missing something. (I've not used Boost.Predef before)  
  
Thanks,  
  
--Beman

---

## Comment 3

> Username: Beman  
> Created_at: 2014-05-27 21:36:07 UTC  
> Url: https://github.com/boostorg/system/pull/3#issuecomment-44338736  

Hum.... I guess I should have written &lt;boost/predef/platform.h&gt;  
  
--Beman

---

## Comment 4

> Username: stgates  
> Created_at: 2014-05-27 21:43:34 UTC  
> Updated_at: 2014-05-27 21:51:37 UTC  
> Url: https://github.com/boostorg/system/pull/3#issuecomment-44339577  

It's transitively pulled in from the pull request I referenced in Boost.Config. All that is needed is to include boost/predef.h.

---

## Comment 5

> Username: Beman  
> Created_at: 2014-05-27 21:49:49 UTC  
> Url: https://github.com/boostorg/system/pull/3#issuecomment-44340222  

On Tue, May 27, 2014 at 5:43 PM, stgates notifications@github.com wrote:  
  
> It's transitively pulled in from the pull request I referenced in  
> Boost.Config. All that is needed is to #include .  
  
I just figured that out. By the way, did you mean to reference a file to  
include?  The markdown on these comments is throwing away anything inside  
&lt; ... &gt;  - instead you have to write ampersand LT semicolon and  
ampersand GT semicolon.  
  
--Beman

---

## Comment 6

> Username: Beman  
> Created_at: 2014-05-27 21:53:05 UTC  
> Url: https://github.com/boostorg/system/pull/3#issuecomment-44340522  

On Tue, May 27, 2014 at 5:49 PM, Beman Dawes bdawes@acm.org wrote:  
  
> On Tue, May 27, 2014 at 5:43 PM, stgates notifications@github.com wrote:  
>   
> > It's transitively pulled in from the pull request I referenced in  
> > Boost.Config. All that is needed is to #include .  
>   
> I just figured that out. By the way, did you mean to reference a file to  
> include?  The markdown on these comments is throwing away anything inside  
> &lt; ... &gt;  - instead you have to write ampersand LT semicolon and  
> ampersand GT semicolon.  
  
Hummm. It is even worse than that. What happens depends on whether you  
respond via email or web interface.  So &lt; ... &gt; didn't get converted  
to < ... > in the above because I had switched to email.. GRRRR.  
  
--Beman

---

## Comment 7

> Username: stgates  
> Created_at: 2014-05-27 21:58:17 UTC  
> Url: https://github.com/boostorg/system/pull/3#issuecomment-44341036  

Yes it got messed up. To be clear here the code as it stands in this pull request will work fine because the boost\predef.h header file is being pulled in transitively from boost\config.hpp.  
  
Once the pull request I have in Boost.Config gets merged this one can as well. Perhaps I could have waited, but I figured send it now and I can address any feedback without have to wait.  
  
Thanks,  
Steve

---
