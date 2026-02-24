# #116 - compilation broken with msvc 14 update 3 [Open]

> Username: RiccardoRossi  
> Created at: 2017-02-26 10:22:06 UTC  
> Updated at: 2018-02-14 00:31:40 UTC  
> Url: https://github.com/boostorg/python/issues/116  

Dear Mantainers,  
  
boost.python is currently broken on msvc 14 update 3.  
  
[http://stackoverflow.com/questions/38261530/unresolved-external-symbols-since-visual-studio-2015-update-3-boost-python-link](url)  
  
I wanted to know if any workaround was being implemented within new versions of boost to avoid that problem.

---

## Comment 1

> Username: SPKorhonen  
> Created at: 2017-02-28 06:09:41 UTC  
> Url: https://github.com/boostorg/python/issues/116#issuecomment-282951670  

The issue has also been reported to MS. For details please see https://connect.microsoft.com/VisualStudio/feedback/details/2852624/regression-when-building-against-boost-python

---

## Comment 2

> Username: RiccardoRossi  
> Created at: 2017-04-16 18:39:21 UTC  
> Url: https://github.com/boostorg/python/issues/116#issuecomment-294367112  

I was wondering if this was this fixed with the 1.64 or if It is still open. Not easy to test for me since i work un Linux or now on msvc14.1, however oír users have 2015 as a requirement so this is effectively a blocker for us

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2017-04-17 02:46:44 UTC  
> Url: https://github.com/boostorg/python/issues/116#issuecomment-294394250  

I'm not aware of any related change in Boost.Python.   
This appears to be a MSVC bug, which I have no idea how to work around. (I'm only a very casual Windows user, though I'd be happy to accept a patch, if it can be demonstrated to work around the issue and not introduce any other problems.

---

## Comment 4

> Username: RiccardoRossi  
> Created at: 2017-04-17 07:07:37 UTC  
> Updated at: 2017-04-17 08:55:38 UTC  
> Url: https://github.com/boostorg/python/issues/116#issuecomment-294421497  

Dear Stefan, thank you for the answer. Unfortunately i do not have a patch...Noþedit]r a clue of how to Fix It.  
  
However should t the bug be announcef d clearly on the boost documentation? I don t know what the boost policy is in these cases, however It effectively blocks the usage of one of the Major compilers in Windows...

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2017-04-17 13:28:45 UTC  
> Url: https://github.com/boostorg/python/issues/116#issuecomment-294485530  

As long as I don't have a clear understanding of what the bug is (and what combination of compiler & code will trigger it) I'm afraid I wouldn't even know what to announce.  
Until then we'll have to fall back to simply answer reports here and wait for someone to either submit a workaround or Microsoft to fix their compiler.

---

## Comment 6

> Username: RiccardoRossi  
> Created at: 2017-04-17 14:11:51 UTC  
> Url: https://github.com/boostorg/python/issues/116#issuecomment-294492755  

Hi again,  
     As posted at the beginning of this thread (link https://connect.microsoft.com/VisualStudio/feedback/details/2852624/regression-when-building-against-boost-python) this affects visual studio 2015 update 3, and affectd to my understanding almost any boost.python code.  
 The link also contains a "workaround") which however implies touching all of the boost.python interfaces.   
It is beyond me to know if the workaround is applicable on the boost side ir if It needs necessary to be on the user side.  
  
It works both on msvc2015 update 2 and visual studio 2017

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2017-04-17 14:20:01 UTC  
> Url: https://github.com/boostorg/python/issues/116#issuecomment-294494211  

The issue you are referring to is marked as "fixed" as of last summer. (And while there is mention of a workaround, I can't see clear instructions as to what needs to happen. I'm certainly not going to change all interfaces for one broken compiler. That's way too invasive.)

---

## Comment 8

> Username: garyfurnish  
> Created at: 2017-08-21 19:16:42 UTC  
> Url: https://github.com/boostorg/python/issues/116#issuecomment-323828093  

I believe this is the same as #148

---

## Comment 9

> Username: RiccardoRossi  
> Created at: 2017-08-21 19:45:07 UTC  
> Url: https://github.com/boostorg/python/issues/116#issuecomment-323834420  

Uff I misunderstood your comment and made a comment in #148  
  
My point is that it used to work with 2017.  
It would be a huge problem if it breaks again, also on the latest release of msvc 😂

---

## Comment 10

> Username: tyanalunas  
> Created at: 2018-02-14 00:31:40 UTC  
> Url: https://github.com/boostorg/python/issues/116#issuecomment-365453966  

@stefanseefeld If you are still looking for a repro case for this bug, it happens when compiling the python bindings for Alembic (PyAlembic):  
https://github.com/alembic/alembic  
  
I was able to author a fix for PyAlembic using the strategy mentioned in the above link:  
https://stackoverflow.com/questions/38261530/unresolved-external-symbols-since-visual-studio-2015-update-3-boost-python-link  
  
Alternatively, I was also able to successfully build by installing the "vanilla" version of msvc14 build tools from 7/19/2015 (not update 3):  
https://my.visualstudio.com/Downloads?q=build%20tools%202015&wt.mc_id=o~msft~vscom~older-downloads
