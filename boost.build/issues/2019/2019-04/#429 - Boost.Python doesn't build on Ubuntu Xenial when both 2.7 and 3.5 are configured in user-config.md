# #429 - Boost.Python doesn't build on Ubuntu Xenial when both 2.7 and 3.5 are configured in user-config [Closed]

> Username: pdimov  
> Created at: 2019-04-16 02:16:27 UTC  
> Updated at: 2019-04-17 18:22:40 UTC  
> Closed at: 2019-04-17 18:22:39 UTC  
> Url: https://github.com/boostorg/build/issues/429  

While investigating #262, I encountered the following problem on Ubuntu Xenial: when `user-config.jam` contains  
```  
using python : 2.7 ;  
using python : 3.5 ;  
```  
then `b2 --with-python` refuses to build anything (same with `b2 --with-python python=2.7`, `b2 --with-python python=3.5`).  
  
When only  
```  
using python : 2.7 ;  
```  
is present, `b2 --with-python` builds. When only  
```  
using python : 3.5 ;  
```  
is present, it builds.  
  
Adding debug output to `python.jam` at https://github.com/boostorg/build/blob/d889d36f85815526ab967c8e1d3db365b7de3c6e/src/tools/python.jam#L1029 and https://github.com/boostorg/build/blob/d889d36f85815526ab967c8e1d3db365b7de3c6e/src/tools/python.jam#L1053 produces...

---

## Comment 1

> Username: pdimov  
> Created at: 2019-04-16 02:20:23 UTC  
> Url: https://github.com/boostorg/build/issues/429#issuecomment-483485550  

```  
alias python_for_extensions : <python>2.7 <target-os>unix <relevant>python : <library>pthread <library>dl <toolset>gcc:<library>util <toolset-intel:platform>linux:<library>util <include>/usr/include/python2.7 <python.interpreter>python2.7  
```  
```  
alias python_for_extensions : <python>3.5 <target-os>unix <relevant>python : <library>pthread <library>dl <toolset>gcc:<library>util <toolset-intel:platform>linux:<library>util <include>/usr/include/python3.5 <python.interpreter>python3.5  
```  
and then  
```  
require-py <address-model>64 <architecture>x86 <asynch-exceptions>off <debug-symbols>off <deduced-address-model>64 <deduced-architecture>x86 <define>NDEBUG <dependency>/boost/python//config-warning <exception-handling>on <extern-c-nothrow>off <hardcode-dll-paths>true <host-os>unix <implicit-dependency>/boost//headers <inlining>full <install-dependencies>off <library>/python//python_for_extensions <link>shared <local-visibility>hidden <optimization>speed <os>UNKNOWN <pch>on <preserve-test-targets>on <profiling>off <python-debugging>off <python>2.7 <relevant>define:<relevant>link <relevant>define:<relevant>python-debugging <relevant>define:<relevant>toolset <relevant>link:<relevant>toolset <relevant>python.interpreter:<relevant>python <relevant>python.interpreter:<relevant>target-os <rtti>on <runtime-debugging>off <runtime-link>shared <stdlib>native <strip>off <suppress-import-lib>false <symlink-location>project-relative <target-os>linux <testing.execute>on <threadapi>pthread <threading>multi <toolset-gcc:version>5.4.0 <toolset>gcc <user-interface>console <variant>release <vectorize>off <visibility>hidden <warnings-as-errors>off <warnings>on  
require-py: py-ext-alternative is null, returning <build>no  
```  
  
I'm not sure if this is a Python or Build issue. Paging @swatanabe. :-)

---

## Comment 2

> Username: pdimov  
> Created at: 2019-04-16 02:24:11 UTC  
> Url: https://github.com/boostorg/build/issues/429#issuecomment-483486220  

(On Windows, multiple versions work.)

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2019-04-16 02:36:15 UTC  
> Url: https://github.com/boostorg/build/issues/429#issuecomment-483488354  

I consider it a `Boost.Build` issue, if only because it is an issue with the build infrastructure only, and I'm unable to fix it. Where you to build `Boost.Python` with `Faber`, however... ;-)

---

## Comment 4

> Username: swatanabe  
> Created at: 2019-04-16 13:10:51 UTC  
> Url: https://github.com/boostorg/build/issues/429#issuecomment-483652855  

AMDG  
  
On 4/15/19 8:20 PM, Peter Dimov wrote:  
> alias python_for_extensions : <python>2.7  
  
> <target-os>unix  
  
Not sure where this is coming from, yet, but  
it's definitely wrong.  It should be linux.  
  
> <relevant>python : <library>pthread <library>dl <toolset>gcc:<library>util <toolset-intel:platform>linux:<library>util <include>/usr/include/python2.7 <python.interpreter>python2.7  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: pdimov  
> Created at: 2019-04-16 14:58:43 UTC  
> Url: https://github.com/boostorg/build/issues/429#issuecomment-483698249  

`<host-os>unix`, `<os>UNKNOWN` too. FWIW, though, if I leave only one `using python : 3.5 ;` in user-config, these properties stay the same, but the build commences. I'll check `b2` from `master` next.

---

## Comment 6

> Username: pdimov  
> Created at: 2019-04-16 15:05:51 UTC  
> Url: https://github.com/boostorg/build/issues/429#issuecomment-483701185  

Boost.Build from master is `<host-os>linux <os>LINUX` in `require-py`, `<target-os>linux` on `python_for_extensions`, and works. So this is caused by @grafikrobot 's changes to `b2`.  
  
I'll transfer this issue to `build` then.

---

## Comment 7

> Username: grafikrobot  
> Created at: 2019-04-17 02:19:55 UTC  
> Url: https://github.com/boostorg/build/issues/429#issuecomment-483911421  

Should be fixed with https://github.com/boostorg/build/commit/0f6f083806d3bcdff9deab45f078b412939a21b9 -- Please try again.

---

## Comment 8

> Username: pdimov  
> Created at: 2019-04-17 18:22:39 UTC  
> Url: https://github.com/boostorg/build/issues/429#issuecomment-484206036  

Confirmed, thanks.
