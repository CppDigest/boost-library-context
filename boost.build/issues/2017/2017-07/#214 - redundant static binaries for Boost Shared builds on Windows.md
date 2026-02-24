# #214 - redundant static binaries for Boost Shared builds on Windows [Open]

> Username: sav-ix  
> Created at: 2017-07-18 16:56:15 UTC  
> Updated at: 2017-12-14 19:40:26 UTC  
> Url: https://github.com/boostorg/build/issues/214  

Hello, everyone,  
  
For Boost Shared builds on Windows (mean `link=shared` build key) static binaries are created in addition to shared. E.g. Boost installation folder for builds using mingw-w64 with Shared layout contain files:  
```  
libboost_chrono-mgw63-mt-1_64.a  <==  
libboost_chrono-mgw63-mt-1_64.dll  
libboost_chrono-mgw63-mt-1_64.dll.a  
libboost_system-mgw63-mt-1_64.a  <==  
libboost_system-mgw63-mt-1_64.dll  
libboost_system-mgw63-mt-1_64.dll.a  
libboost_timer-mgw63-mt-1_64.a  <==  
libboost_timer-mgw63-mt-1_64.dll  
libboost_timer-mgw63-mt-1_64.dll.a  
libboost_unit_test_framework-mgw63-mt-1_64.a  <==  
libboost_unit_test_framework-mgw63-mt-1_64.dll  
libboost_unit_test_framework-mgw63-mt-1_64.dll.a  
```  
  
and using ICC or MSVC:  
```  
boost_chrono-vc140-mt-1_64.dll  
boost_chrono-vc140-mt-1_64.lib  
boost_system-vc140-mt-1_64.dll  
boost_system-vc140-mt-1_64.lib  
boost_timer-vc140-mt-1_64.dll  
boost_timer-vc140-mt-1_64.lib  
boost_unit_test_framework-vc140-mt-1_64.dll  
boost_unit_test_framework-vc140-mt-1_64.lib  
libboost_chrono-vc140-mt-1_64.lib  <==  
libboost_system-vc140-mt-1_64.lib  <==  
libboost_timer-vc140-mt-1_64.lib  <==  
libboost_unit_test_framework-vc140-mt-1_64.lib  <==  
```  
Reproduced for Boost.Chrono, Boost.System, Boost.Timer, Boost.Unit_test_framework modules only.  
  
~~Could this static builds be removed from Boost Shared builds?~~  
Could rules for building static binaries for Shared builds be removed from Boost build system?  
  
Environment:  
- Windows 10 x64,  
- mingw-w64 6.3.0  
- Intel Parallel Studio XE 2017 Update 4,  
- Visual Studio 2015 Update 3,  
- BOOST-1.64.0.  
  
  
Best,  
  
Alexander

---

## Comment 1

> Username: sav-ix  
> Created at: 2017-07-18 17:27:59 UTC  
> Updated at: 2017-07-18 17:50:27 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316136703  

It's understandable, that import libraries required for linking with shared binaries. Meaning of this issue, there is no need in *static* binaries for Shared builds, when *shared* binaries available.  
Updated issue description in order to clarify its goal.

---

## Comment 2

> Username: sav-ix  
> Created at: 2017-07-18 17:47:46 UTC  
> Updated at: 2017-07-18 18:23:11 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316142399  

Raffi, let me clarify my question concerning Boost.Test.  
For Boost builds using <mingw-w64|ICC|MSVC> with Shared layout, Boost.Test binaries are build with *static* layout only. E.g. *shared* binaries of this module not exists in Boost installation folder.  
The same applies to Boost.Exceptions module. Thus me also asked @zajo to shed lite, whether it's a bug or feature of Boost.Exceptions module.  
Would be appreciated for clarification.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2017-07-18 18:31:14 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316155745  

@sav-ix I initially misunderstood your concerns (this is why I deleted my comments thinking I am an idiot :) ). As a side note, what are the sizes of the spurious `.lib` ?

---

## Comment 4

> Username: sav-ix  
> Created at: 2017-07-18 18:57:02 UTC  
> Updated at: 2017-07-18 19:10:39 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316163619  

E.g. installation folder of MSVC build with Release Shared configuration contain files:  
```  
boost_chrono-vc140-mt-1_64.lib  <== import library (24.1 KB)  
libboost_chrono-vc140-mt-1_64.lib  <== static library (374 KB)  
```  
  
which relate to the issue with redundant static binaries, and files:  
```  
libboost_exception-vc140-mt-1_64.lib  <== static library (1.41 KB)  
libboost_test_exec_monitor-vc140-mt-1_64.lib  <== static library (16.0 MB)  
```  
which relate to the issue with missing shared binaries.  
  
If content of mentioned above static libraries extracted using archiver, e.g. 7-ZIP, path to extracted `.obj` files contain folder `.../link-static/...`, which is additional proof, that it's a *static* binaries.

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2017-07-18 19:16:46 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316168759  

After digging a bit on boost.test side, if I remove `boost_test_exec_monitor` from `./libs/test/build/Jamfile.v2` then the spurious static library targets `chrono`, `system` and `timer` are not generated (narroing with `--with-test` at top level). The `boost_test_exec_monitor` is always static.  
  
However, I thought it was the responsibility of the `boost-install` variable in the `Jamfile.v2` to determine the targets to install. If I remove `boost_test_exec_monitor`, then the same spurious static libraries are installed.   
  
It seems that all the `lib` targets in the `Jamfile.v2` are always generated. Any hints and best practices welcome.

---

## Comment 6

> Username: swatanabe  
> Created at: 2017-07-18 19:18:05 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316169077  

AMDG  
  
On 07/18/2017 10:56 AM, sav-ix wrote:  
> Hello, everyone,  
>   
> For Boost Shared builds on Windows (mean `link=shared` build key) static binaries are created in addition to shared. E.g. Boost installation folder for builds using mingw-w64 with Shared layout contain files:  
> ```  
> libboost_chrono-mgw63-mt-1_64.a  <==  
> libboost_system-mgw63-mt-1_64.a  <==  
> libboost_timer-mgw63-mt-1_64.a  <==  
> libboost_unit_test_framework-mgw63-mt-1_64.a  <==  
> ```  
>   
  
  boost_test_exec_monitor explicitly forces static linking (possibly  
because dynamic linking doesn't work for some reason?), which  
causes dependent libraries (time -> chrono&system) to be built  
as static as well.  I'm not sure why boost_unit_test_framework  
is built as a static library.  
  
In Christ,  
Steven Watanabe

---

## Comment 7

> Username: raffienficiaud  
> Created at: 2017-07-18 19:22:12 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316170145  

@swatanabe On OSX at least the `boost_unit_test_framework` is not built as static library if `link=shared` is specified (or at least not installed inside the `--prefix`).

---

## Comment 8

> Username: swatanabe  
> Created at: 2017-07-18 19:26:41 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316171174  

AMDG  
  
On 07/18/2017 01:16 PM, Raffi Enficiaud wrote:  
> After digging a bit on boost.test side, if I remove `boost_test_exec_monitor` from `./libs/test/build/Jamfile.v2` then the spurious static library targets `chrono`, `system` and `timer` are not generated (narroing with `--with-test` at top level). The `boost_test_exec_monitor` is always static.  
>   
> However, I thought it was the responsibility of the `boost-install` variable in the `Jamfile.v2` to determine the targets to install. If I remove `boost_test_exec_monitor`, then the same spurious static libraries are installed.   
>   
> It seems that all the `lib` targets in the `Jamfile.v2` are always generated. Any hints and best practices welcome.  
>   
  
boost-install doesn't actually do anything useful.  It creates  
an install target in your project (which no one ever uses).  Jamroot  
picks up everything that is built by b2 libs/test/build.  To disable  
it you can mark it as explicit.  
  
In Christ,  
Steven Watanabe

---

## Comment 9

> Username: raffienficiaud  
> Created at: 2017-07-18 19:34:19 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316173099  

@swatanabe thanks for the clarifications! indeed adding `explicit boost_test_exec_monitor ;` to the `Jamfile.v2` removes those static libraries from the installation prefix.  
  
@sav-ix would you please try on your side?   
OTOH, I do not know what this will eventually break, this code a quite old/legacy and I do not know who in the work is using it. I would not go there for 1.65.

---

## Comment 10

> Username: swatanabe  
> Created at: 2017-07-18 19:39:32 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316174747  

AMDG  
  
On 07/18/2017 01:22 PM, Raffi Enficiaud wrote:  
> @swatanabe On OSX at least the `boost_unit_test_framework` is not built as static library if `link=shared` is specified (or at least not installed inside the `--prefix`).  
>   
  
If I use --with-test, then I also get only the shared library.  
However, building all libraries gives me the static library  
as well, which indicates that some other library is probably  
causing this.  
  
In Christ,  
Steven Watanabe

---

## Comment 11

> Username: sav-ix  
> Created at: 2017-07-18 19:41:23 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316175343  

Raffi, could you attach `.diff` file or link to PR, which I should apply to Boost sources? So we would surely test the same code on our systems.

---

## Comment 12

> Username: swatanabe  
> Created at: 2017-07-18 19:43:08 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316175814  

AMDG  
  
On 07/18/2017 01:34 PM, Raffi Enficiaud wrote:  
>   
> @sav-ix would you please try on your side?   
> OTOH, I do not know what this will eventually break, this code a quite old/legacy and I do not know who in the work is using it. I would not go there for 1.65.  
>   
  
  It might be better to use <link>shared:<build>no  
on boost_test_exec_monitor, to skip the shared lib  
instead of switching to static.  
  
In Christ,  
Steven Watanabe

---

## Comment 13

> Username: raffienficiaud  
> Created at: 2017-07-18 19:45:01 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316176271  

@sav-ix there as a git patch  
[0001-Making-the-boost_test_exec_monitor-explicit.patch.txt](https://github.com/boostorg/build/files/1157097/0001-Making-the-boost_test_exec_monitor-explicit.patch.txt)

---

## Comment 14

> Username: raffienficiaud  
> Created at: 2017-07-18 19:47:56 UTC  
> Updated at: 2017-07-18 19:48:15 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316176984  

@swatanabe well, the question is that I do not know if this library (subset of boost.test) should even be compiled as a shared library (although it contains `<link>shared:<define>BOOST_TEST_DYN_LINK=1`, I believe this has never been well tested).  
  
I will try of course!

---

## Comment 15

> Username: sav-ix  
> Created at: 2017-07-18 20:10:15 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316182768  

Applied your patch to [Boost-1.65.0_beta1_rc2](https://dl.bintray.com/boostorg/beta/1.65.0.beta.1/source/boost_1_65_0_beta1_rc2.tar.gz) sources.  
For builds using MSVC with Release *Shared* configuration, file `libboost_test_exec_monitor-vc140-mt-1_65.lib` wasn't build and missing in Boost installation folder. Patch didn't help to build shared version of this module.  
For builds using MSVC with Release *Static* configuration, file `libboost_test_exec_monitor-vc140-mt-s-1_65.lib` wasn't build and missing in Boost installation folder (no such troubles before patch).  
All redundant static binaries from 1st post were built as before.

---

## Comment 16

> Username: sav-ix  
> Created at: 2017-07-18 20:16:23 UTC  
> Updated at: 2017-07-18 20:21:49 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316184369  

> shared library ... I believe this has never been well tested  
  
Always wondered, why [Boost test matrix](http://www.boost.org/development/tests/master/developer/summary.html) run tests for *static* binaries only. For Shared-only issues it took lot of time prove to Boost Developers, that problem exists.

---

## Comment 17

> Username: raffienficiaud  
> Created at: 2017-07-18 20:20:45 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316185511  

@sav-ix it does not test only static binaries, it tests many variants.   
  
By "tests", I am also referring to what is outside `boost.test`. As I said, I do not know exactly why this library exists, but I believe only the static variant is really supported or make sense.   
  
> Patch didn't help to build shared version of this module.  
  
This follows my previous remark.  
  
> For builds using MSVC with Release Static configuration, file libboost_test_exec_monitor-vc140-mt-s-1_65.lib wasn't build and missing  
  
This is not good :)

---

## Comment 18

> Username: raffienficiaud  
> Created at: 2017-07-18 20:21:43 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316185772  

Anyway, the post of @swatanabe suggests that there another link to `boost_test_exec_monitor` somewhere else, I will dig into it.

---

## Comment 19

> Username: sav-ix  
> Created at: 2017-07-18 20:34:16 UTC  
> Updated at: 2017-07-20 07:14:35 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316189125  

> @sav-ix it does not test only static binaries, it tests many variants.  
  
I had few examples, when Shared-only issues weren't catch by Boost test matrix (e.g. https://github.com/boostorg/context/issues/16 , https://github.com/boostorg/serialization/pull/42).  
This could be possible only and only in case, when *shared* binaries weren't tested. This proves, that problem with Boost *shared* binaries testing exists. At least for Windows builds using mingw-w64.

---

## Comment 20

> Username: swatanabe  
> Created at: 2017-07-18 22:56:30 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-316221890  

AMDG  
  
On 07/18/2017 02:21 PM, Raffi Enficiaud wrote:  
> Anyway, the post of @swatanabe suggests that there another link to `boost_test_exec_monitor` somewhere else, I will dig into it.  
>   
  
  I found it.  It's coming from metaparse (which  
is header only and shouldn't have a build/ directory  
at all).  
  
In Christ,  
Steven Watanabe

---

## Comment 21

> Username: sav-ix  
> Created at: 2017-11-22 21:28:13 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-346477973  

After moving from [1.65.1](https://dl.bintray.com/boostorg/release/1.65.1/source/boost_1_65_1.tar.gz) to [1.66.0.b1.rc2](https://dl.bintray.com/boostorg/beta/1.66.0.beta.1/source/boost_1_66_0_b1_rc2.tar.gz) found, that shared builds using mingw-w64 doesn't contain static binary  
```  
libboost_unit_test_framework-mgw72-mt-1_65_1.a  
```  
and shared builds using MSVC doesn't contain static binary  
```  
libboost_unit_test_framework-vc141-mt-1_65_1.lib  
```  
Does it mean, that you succeed to find a solution for this issue? If yes, can it be applied to other Boost modules too?

---

## Comment 22

> Username: swatanabe  
> Created at: 2017-12-14 19:40:25 UTC  
> Url: https://github.com/boostorg/build/issues/214#issuecomment-351814702  

AMDG  
  
On 11/22/2017 02:28 PM, sav-ix wrote:  
> After moving from [1.65.1](https://dl.bintray.com/boostorg/release/1.65.1/source/boost_1_65_1.tar.gz) to [1.66.0.b1.rc2](https://dl.bintray.com/boostorg/beta/1.66.0.beta.1/source/boost_1_66_0_b1_rc2.tar.gz) found, that shared builds using mingw-w64 doesn't contain static binary  
> ```  
> libboost_unit_test_framework-mgw72-mt-1_65_1.a  
> ```  
> and shared builds using MSVC doesn't contain static binary  
> ```  
> libboost_unit_test_framework-vc141-mt-1_65_1.lib  
> ```  
> Does it mean, that you succeed to find a solution for this issue? If yes, can it be applied to other Boost modules too?  
>   
  
It seems to have been fixed by:  
https://github.com/boostorg/metaparse/pull/9  
  
In Christ,  
Steven Watanabe
