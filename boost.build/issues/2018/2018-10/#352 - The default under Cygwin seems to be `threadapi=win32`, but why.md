# #352 - The default under Cygwin seems to be `threadapi=win32`, but why? [Closed]

> Username: pdimov  
> Created at: 2018-10-11 13:20:46 UTC  
> Updated at: 2023-12-24 03:03:20 UTC  
> Closed at: 2023-12-24 03:03:20 UTC  
> Url: https://github.com/boostorg/build/issues/352  

While investigating https://github.com/boostorg/thread/issues/228 I saw that under Cygwin `threadapi=win32` is chosen by default, but I can't figure out why. The `get-default` rule in `threadapi-feature.jam` checks `target-os = windows`, and this is `cygwin` under Cygwin, so if this `get-default` implementation is actually used, it should choose `pthread`. I don't see it being used though. But if the default is always `win32`, how does it work under Posix platforms?

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-10-11 14:36:17 UTC  
> Url: https://github.com/boostorg/build/issues/352#issuecomment-428979594  

AMDG  
  
On 10/11/2018 07:20 AM, Peter Dimov wrote:  
> While investigating https://github.com/boostorg/thread/issues/228 I saw that under Cygwin `threadapi=win32` is chosen by default, but I can't figure out why. The `get-default` rule in `threadapi-feature.jam` checks `target-os = windows`, and this is `cygwin` under Cygwin, so if this `get-default` implementation is actually used, it should choose `pthread`. I don't see it being used though. But if the default is always `win32`, how does it work under Posix platforms?  
>   
  
There is no default, because it's an optional feature.  
Jamroot has <conditional>@threadapi.detect.  If it's  
being set to win32, the reason is most likely because  
target-os is also set to windows.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: pdimov  
> Created at: 2018-10-11 15:02:34 UTC  
> Url: https://github.com/boostorg/build/issues/352#issuecomment-428989731  

`target-os` is `cygwin` though.  
  
```  
C:\boost-git\develop\libs\thread>b2 test toolset=gcc  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : yes (cached)  
...patience...  
...patience...  
...patience...  
...found 16671 targets...  
...updating 7482 targets...  
gcc.compile.c++ ..\..\bin.v2\libs\thread\build\gcc-7.3.0\debug\target-os-cygwin\  
threadapi-win32\threading-multi\visibility-hidden\win32\thread.o  
..\..\libs\thread\src\win32\thread.cpp: In function 'void boost::{anonymous}::cr  
eate_current_thread_tls_key()':  
..\..\libs\thread\src\win32\thread.cpp:86:13: error: 'tss_cleanup_implemented' w  
as not declared in this scope  
             tss_cleanup_implemented(); // if anyone uses TSS, we need the clean  
up linked in  
             ^~~~~~~~~~~~~~~~~~~~~~~  
```

---

## Comment 3

> Username: swatanabe  
> Created at: 2018-10-11 15:18:58 UTC  
> Url: https://github.com/boostorg/build/issues/352#issuecomment-428996032  

AMDG  
  
On 10/11/2018 09:10 AM, Peter Dimov wrote:  
> `target-os` is `cygwin` though.  
>   
  
Ugh.  This sounds like yet another bug in  
targets.evaluate-requirements.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: swatanabe  
> Created at: 2018-10-11 15:52:56 UTC  
> Url: https://github.com/boostorg/build/issues/352#issuecomment-429009773  

AMDG  
  
Okay.  I know what's going on.  The implementation  
of threadapi is not quite correct, unfortunately.  
  
a) target-os is set to the default value (windows)  
b) threadapi is calculated based on <target-os>windows  
c) target-os is corrected and changes to cygwin.  
d) threadapi-feature.detect carries over the value  
   calculated in (b), instead of recalculating it.  
  
The core issue is that target requirements are  
intended to set requirements rather than default  
values and the algorithm for evaluating conditionals  
doesn't quite work when we try to fudge it be setting  
a requirement iff it is not already set.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: pdimov  
> Created at: 2018-10-11 16:04:43 UTC  
> Url: https://github.com/boostorg/build/issues/352#issuecomment-429014649  

Would moving the threadapi detection to default-build fix it?

---

## Comment 6

> Username: swatanabe  
> Created at: 2018-10-11 16:15:10 UTC  
> Url: https://github.com/boostorg/build/issues/352#issuecomment-429018567  

AMDG  
  
On 10/11/2018 10:04 AM, Peter Dimov wrote:  
> Would moving the threadapi detection to default-build fix it?  
>   
  
No.  default-build doesn't handle conditionals.  
(I'd like it to work, but it would make an already  
complex system even more complicated.  In particular,  
I haven't considered how this would interact with  
the fact that the default-build allows multiple values  
of a feature.)  
  
I /think/ that the following should work:  
a) Get rid of the default-value handling in threadapi-feature.detect  
   so the rule expresses a strict requirement.  
b) Pass it to toolset.add-defaults.  
c) Remove the reference to it from Jamroot.  
  
In Christ,  
Steven Watanabe

---

## Comment 7

> Username: pdimov  
> Created at: 2018-10-11 16:37:06 UTC  
> Url: https://github.com/boostorg/build/issues/352#issuecomment-429026921  

If we drop the default value handling in `threadapi-feature.detect`, it would just return `<threadapi>foo` when `<threadapi>` is already `foo`, so there's not much point in having it at all, is there?  
  
Won't just replacing `<conditional>@threadapi-feature.detect` with `<target-os>windows:<threadapi>win32` in Jamroot do the trick?  
  
(Ignoring the <relevant> business at the moment.)

---

## Comment 8

> Username: pdimov  
> Created at: 2018-10-11 16:37:32 UTC  
> Url: https://github.com/boostorg/build/issues/352#issuecomment-429027090  

Addendum: no, it doesn't:  
  
```  
error: No best alternative for ../../libs/thread/build/thread_sources  
    next alternative: required properties: <threadapi>win32 <threading>multi  
        not matched  
    next alternative: required properties: <threadapi>pthread <threading>multi  
        not matched  
```

---

## Comment 9

> Username: pdimov  
> Created at: 2018-10-11 16:44:00 UTC  
> Url: https://github.com/boostorg/build/issues/352#issuecomment-429029552  

```  
      <threadapi>pthread  
      <target-os>windows:<threadapi>win32  
```  
  
works though, except for  
  
```  
************************************************************  
Trying to build Boost.Thread with pthread support.  
If you need pthread you should specify the paths.  
You can specify them in site-config.jam, user-config.jam  
or in the environment.  
For example:  
PTW32_INCLUDE=C:\Program Files\ptw32\Pre-built2\include  
PTW32_LIB=C:\Program Files\ptw32\Pre-built2\lib  
************************************************************  
```  
  
from the Thread Jamfile, which at configure time doesn't yet know that we'll be using win32 after all.

---

## Comment 10

> Username: pdimov  
> Created at: 2018-10-11 16:50:07 UTC  
> Url: https://github.com/boostorg/build/issues/352#issuecomment-429031818  

I suppose that `win32_pthread_paths` should be somehow using `message` to warn at build time instead of warning at configure time, but I have no idea how this would be expressed.  
  
Or, we need the `!condition:requirement` syntax after all, to use `!<target-os>windows:<threadapi>pthread>` instead.

---

## Comment 11

> Username: swatanabe  
> Created at: 2018-10-11 16:55:32 UTC  
> Url: https://github.com/boostorg/build/issues/352#issuecomment-429033933  

AMDG  
  
On 10/11/2018 10:37 AM, Peter Dimov wrote:  
> If we drop the default value handling in `threadapi-feature.detect`, it would just return `<threadapi>foo` when `<threadapi>` is already `foo`, so there's not much point in having it at all, is there?  
>   
  
I actually meant the other way around.  Remove the  
part that returns foo when <threadapi>foo is already  
present.  
  
> Won't just replacing `<conditional>@threadapi-feature.detect` with `<target-os>windows:<threadapi>win32` in Jamroot do the trick?  
>   
  
That will prevent users from overriding the default with  
`b2 threadapi=win32`.  
  
> (Ignoring the <relevant> business at the moment.)  
>   
  
<relevant> is only needed for <conditional>.  If you're  
using a regular conditional property, it'll be deduced.  
  
In Christ,  
Steven Watanabe

---

## Comment 12

> Username: swatanabe  
> Created at: 2018-10-11 17:10:32 UTC  
> Url: https://github.com/boostorg/build/issues/352#issuecomment-429039650  

On 10/11/2018 10:50 AM, Peter Dimov wrote:  
> I suppose that `win32_pthread_paths` should be somehow using `message` to warn at build time instead of warning at configure time, but I have no idea how this would be expressed.  
>   
  
Right.  Conditionals might be evaluated with totally  
crazy parameters.  The only thing that Boost.Build  
guarantees is that it will eventually converge to something  
sane.  Putting warning messages inside a <conditional>  
is a bad idea.  Returning a <dependency> on a message  
target will probably do the trick.  
  
> Or, we need the `!condition:requirement` syntax after all, to use `!<target-os>windows:<threadapi>pthread>` instead.  
>   
  
I'm testing a patch for Boost.Build right now.  
  
Summary:  
feature threadapi : pthread win32 : propagated symmetric ;  
toolset.add-defaults <target-os>windows:<threadapi>win32 ;  
  
Notes:  
- I added symmetric to preserve the existing paths (without  
  it the threadapi-pthread subdirectory would not be created.)  
- Removing optional and listing pthread first makes pthread the  
  default value.  toolset.add-defaults takes priority over  
  this default.  
- This effectively puts the default handling directly in the  
  feature definition (where it really belongs) and no longer  
  requires Jamroot to invoke anything explicitly.  
  
In Christ,  
Steven Watanabe

---

## Comment 13

> Username: pdimov  
> Created at: 2018-10-11 17:20:06 UTC  
> Url: https://github.com/boostorg/build/issues/352#issuecomment-429042754  

OK, I'll wait for your patch. At the moment I've been trying  
  
```  
rule detect ( properties * )  
{  
    local ps = [ property-set.create $(properties) ] ;  
    local api = [ $(ps).get <threadapi> ] ;  
  
    local r ;  
  
    if ! $(api)  
    {  
        r += "<threadapi>pthread" ;  
        r += "<target-os>windows:<threadapi>pthread" ;  
        r += "<relevant>threadapi:<relevant>target-os" ;  
    }  
  
    return $(r) ;  
}  
```  
  
in threadapi-feature.jam and this errors out at the following line in thread/build/Jamfile:  
  
```  
        if ! [ configure.builds has_atomic_flag_lockfree   
            : $(properties) : "lockfree boost::atomic_flag" ]  {   
           result += <library>/boost/atomic//boost_atomic ;   
        }   
```  
  
with  
  
```  
C:/boost-git/develop/tools/build/src/build\feature.jam:491: in validate-value-string from module feature  
error: "windows:<threadapi>pthread" is not a known value of feature <target-os>  
```  
  
Apparently `configure.builds` doesn't like conditional requirements.

---

## Comment 14

> Username: swatanabe  
> Created at: 2018-10-11 17:28:50 UTC  
> Url: https://github.com/boostorg/build/issues/352#issuecomment-429045863  

AMDG  
  
On 10/11/2018 11:20 AM, Peter Dimov wrote:  
>   
> Apparently `configure.builds` doesn't like conditional requirements.  
>   
  
A <conditional> cannot return another conditional.  
  
In Christ,  
Steven Watanabe

---

## Comment 15

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:15 UTC  
> Url: https://github.com/boostorg/build/issues/352#issuecomment-868936392  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
