# #288 - Mysterious unnecessary rebuilds under MSVC [Closed]

> Username: pdimov  
> Created at: 2018-01-19 16:16:36 UTC  
> Updated at: 2018-01-23 03:52:34 UTC  
> Closed at: 2018-01-23 03:52:34 UTC  
> Url: https://github.com/boostorg/build/issues/288  

I noticed that when running some Boost test suites, repeated `b2` invocations always rebuild some targets, without a discernible reason.  
  
After looking at it for a while, what I think happens is that the MS linker, when creating a DLL and an import LIB, does not change the timestamp of the import LIB if its contents haven't changed.  
  
Since the dependency check looks at the LIB timestamp in addition to the DLL, it always sees it as outdated and rebuilds it each time, but it doesn't help.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-01-19 16:33:58 UTC  
> Url: https://github.com/boostorg/build/issues/288#issuecomment-359019150  

AMDG  
  
On 01/19/2018 09:16 AM, Peter Dimov wrote:  
> I noticed that when running some Boost test suites, repeated `b2` invocations always rebuild some targets, without a discernible reason.  
>   
> After looking at it for a while, what I think happens is that the MS linker, when creating a DLL and an import LIB, does not change the timestamp of the import LIB if its contents haven't changed.  
>   
> Since the dependency check looks at the LIB timestamp in addition to the DLL, it always sees it as outdated and rebuilds it each time, but it doesn't help.  
>   
  
I'm sure that this has come up before and I vaguely  
remember trying to fix it.  
  
Anyway, I think these modifiers should capture  
the situation correctly:  
  
INCLUDES x.lib : x.dll ;  
NOUPDATE x.lib ;  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: swatanabe  
> Created at: 2018-01-20 16:49:16 UTC  
> Url: https://github.com/boostorg/build/issues/288#issuecomment-359184893  

AMDG  
  
On 01/19/2018 09:16 AM, Peter Dimov wrote:> After looking at it for a  
while, what I think happens is that the MS linker, when creating a DLL  
and an import LIB, does not change the timestamp of the import LIB if  
its contents haven't changed.  
>   
  
It looks like someone tried to work around this:  
https://github.com/boostorg/build/blob/develop/src/tools/msvc.jam#L567  
  
So, is this a change in the linker or is the test actually  
using incremental linking?  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: pdimov  
> Created at: 2018-01-20 16:53:32 UTC  
> Url: https://github.com/boostorg/build/issues/288#issuecomment-359185200  

I'm not sure how a test would be using incremental linking even if it wanted to. Is there a way to enable it?

---

## Comment 4

> Username: pdimov  
> Created at: 2018-01-20 17:04:16 UTC  
> Url: https://github.com/boostorg/build/issues/288#issuecomment-359185907  

It does appear to be a change in the linker. msvc-8.0 does not rebuild, msvc-14.1 does. Steps to reproduce: in `libs/system/test`, do `b2 toolset=msvc-14.1`, then once more, then once more until it stops building things; `touch throw_test.cpp`; `b2` again a few times.

---

## Comment 5

> Username: pdimov  
> Created at: 2018-01-20 17:11:59 UTC  
> Url: https://github.com/boostorg/build/issues/288#issuecomment-359186478  

The change is in msvc-14.1; versions up to and including msvc-14.0 don't rebuild.  
  
Incidentally, the second `b2` invocation in the above steps performs  
  
```  
C:\boost-git\develop\libs\system\test>b2 -j8 toolset=msvc-14.0  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
...patience...  
...found 584 targets...  
...updating 3 targets...  
msvc.write-setup-script ..\..\..\bin.v2\standalone\msvc\msvc-14.0\address-model-  
32\architecture-x86\msvc-setup.bat  
...updated 4 targets...  
```  
  
which is also a bit odd.

---

## Comment 6

> Username: swatanabe  
> Created at: 2018-01-20 17:27:50 UTC  
> Url: https://github.com/boostorg/build/issues/288#issuecomment-359187546  

AMDG  
  
On 01/20/2018 10:11 AM, Peter Dimov wrote:  
> The change is in msvc-14.1; versions up to and including msvc-14.0 don't rebuild.  
>   
  
Okay.  Fix incoming shortly.  
  
> Incidentally, the second `b2` invocation in the above steps performs  
>   
> ```  
> C:\boost-git\develop\libs\system\test>b2 -j8 toolset=msvc-14.0  
> Performing configuration checks  
>   
>     - default address-model    : 32-bit (cached)  
>     - default architecture     : x86 (cached)  
>     - symlinks supported       : no  (cached)  
>     - junctions supported      : yes (cached)  
>     - hardlinks supported      : yes (cached)  
> ...patience...  
> ...found 584 targets...  
> ...updating 3 targets...  
> msvc.write-setup-script ..\..\..\bin.v2\standalone\msvc\msvc-14.0\address-model-  
> 32\architecture-x86\msvc-setup.bat  
> ...updated 4 targets...  
> ```  
>   
> which is also a bit odd.  
>   
  
  That one happens because the script gets created  
in two different places, depending on whether the  
architecture/address-model tests are actually run.  
This is a side effect of a minor mistake I made changing:  
  
- 64-bit : no  
- 32-bit : yes  
to  
- default address-model : 32-bit  
  
(I forgot to actualize the target if it's cached.)  
  
  I guess the best solution for that would be  
to directly compute a unique path for each  
script variation in msvc.jam, instead of using  
the default property-based path.  
  
In Christ,  
Steven Watanabe

---

## Comment 7

> Username: pdimov  
> Created at: 2018-01-21 20:03:10 UTC  
> Url: https://github.com/boostorg/build/issues/288#issuecomment-359276192  

With the change, the msvc-14.1 rebuilds are fixed; but now, a DLL that exports no symbols, and therefore produces no LIB, is relinked each time.  
  
Although I'm not entirely sure whether this wasn't already the case before. This is also somewhat of a corner case, which I only encountered just now because I forgot the `__declspec(export)`. Still, FWIW.

---

## Comment 8

> Username: swatanabe  
> Created at: 2018-01-21 20:13:44 UTC  
> Url: https://github.com/boostorg/build/issues/288#issuecomment-359276927  

AMDG  
  
On 01/21/2018 01:03 PM, Peter Dimov wrote:  
> With the change, the msvc-14.1 rebuilds are fixed; but now, a DLL that exports no symbols, and therefore produces no LIB, is relinked each time.  
>   
> Although I'm not entirely sure whether this wasn't already the case before.  
  
It's always been that way.  
  
See also: https://github.com/boostorg/build/pull/28  
  
> This is also somewhat of a corner case, which I only encountered just now because I forgot the `__declspec(export)`. Still, FWIW.  
>   
  
In Christ,  
Steven Watanabe
