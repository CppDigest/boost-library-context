# #266 - [ ac.check-library /libjpeg//libjpeg : <library>/libjpeg//libjpeg : <build>no ] fails [Closed]

> Username: stefanseefeld  
> Created at: 2017-11-23 13:22:45 UTC  
> Updated at: 2018-01-12 00:03:15 UTC  
> Closed at: 2018-01-12 00:03:15 UTC  
> Url: https://github.com/boostorg/build/issues/266  

I'm trying to use the b2 `libjpeg` module with Boost.GIL, but run into problems.  
The `ac.check-library` test fails, as the generated source file fails to compile:  
`g++ -c -o jpeglib.o /usr/include/jpeglib.h` results in   
"/usr/include/jpeglib.h:792:3: error: ‘size_t’ does not name a type  
..."  
  
(This can probably fixed by injecting a `#include <stdlib.h>` before including the jpeglib.h header.)

---

## Comment 1

> Username: swatanabe  
> Created at: 2017-11-24 02:52:57 UTC  
> Url: https://github.com/boostorg/build/issues/266#issuecomment-346730639  

AMDG  
  
On 11/23/2017 06:22 AM, Stefan Seefeld wrote:  
> I'm trying to use the b2 `libjpeg` module with Boost.GIL, but run into problems.  
> The `ac.check-library` test fails, as the generated source file fails to compile:  
> `g++ -c -o jpeglib.o /usr/include/jpeglib.h` results in   
> "/usr/include/jpeglib.h:792:3: error: ‘size_t’ does not name a type  
> ..."  
>   
> (This can probably fixed by injecting a `#include <stdlib.h>` before including the jpeglib.h header.)  
>   
  
  This is quite unfortunate.  The code that causes this is  
generic code for testing whether a header exists.  I'll  
have to pass an extra parameter through the whole system  
to allow jpeg to have a special variation.  Have you  
reported this to the libjpeg devlopers?  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2017-11-24 02:56:13 UTC  
> Url: https://github.com/boostorg/build/issues/266#issuecomment-346731154  

Yes, I feel with you !  
There is a reason for all the complexity in the GNU autotools. And while I would hope that for the majority of cases the simple approach you currently use would continue to work, there clearly needs to be a way to extend the logic to add additional code (or prerequisite headers, etc., etc.) to test a library's usability.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2017-11-24 02:59:24 UTC  
> Url: https://github.com/boostorg/build/issues/266#issuecomment-346731631  

(To answer your question: No, I haven't reported it, as I'm not even sure this qualifies as a bug. Yes, it would be great if such a header would be valid stand-alone, but in the real world there are many cases where preconditions of various kinds need to be met before a third-party header may be included.)

---

## Comment 4

> Username: swatanabe  
> Created at: 2017-11-24 04:01:56 UTC  
> Url: https://github.com/boostorg/build/issues/266#issuecomment-346738566  

AMDG  
  
On 11/23/2017 07:59 PM, Stefan Seefeld wrote:  
> (To answer your question: No, I haven't reported it, as I'm not even sure this qualifies as a bug. Yes, it would be great if such a header would be valid stand-alone, but in the real world there are many cases where preconditions of various kinds need to be met before a third-party header may be included.)  
>   
  
  Well, it seems to be intentional as the documentation claims  
that you must #include <stdio.h> first.  However, there doesn't  
seem to be any good reason for it.  The code is quite confusing  
and very poorly written.  It might have made sense a few decades  
ago if the authors didn't want to go to the trouble of finding  
the correct header on very old systems, but on the other hand,  
it's impossible to build the library without knowing the correct  
header to include, so, of course, the package contains a header  
specifically for this purpose... which for some reason is  
not #included by jpeglib.h.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: pdimov  
> Created at: 2017-11-28 17:22:31 UTC  
> Url: https://github.com/boostorg/build/issues/266#issuecomment-347597660  

https://github.com/boostorg/build/commit/50f13ef34a2f17e1b703f740f7b915c5301ebda0 fixes this by the look of it.

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2017-12-07 02:56:29 UTC  
> Url: https://github.com/boostorg/build/issues/266#issuecomment-349847579  

Indeed it does. Any plans to merge that to master ?

---

## Comment 7

> Username: swatanabe  
> Created at: 2017-12-07 03:48:42 UTC  
> Url: https://github.com/boostorg/build/issues/266#issuecomment-349854827  

AMDG  
  
On 12/06/2017 07:56 PM, Stefan Seefeld wrote:  
> Indeed it does. Any plans to merge that to master ?  
>   
  
After 1.66 ships.  
  
In Christ,  
Steven Watanabe
