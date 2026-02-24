# #659 - Default address-model misdetected in AppVeyor CI (32-bit MSVC 9 to 12) [Open]

> Username: Lastique  
> Created at: 2020-09-24 00:34:48 UTC  
> Updated at: 2021-05-29 16:23:01 UTC  
> Url: https://github.com/boostorg/build/issues/659  

Here is an example of the failing build of Boost.Filesystem:  
  
https://ci.appveyor.com/project/Lastique/filesystem/build/job/bpkhq90n0tot6k24  
  
This build was run on https://github.com/boostorg/filesystem/commit/e260f134d4971a7c130d1cb46e273c2d0140ef87 with the following command line:  
  
```  
b2 -j 4 libs/filesystem/test toolset=msvc-9.0,msvc-10.0,msvc-11.0,msvc-12.0  
```  
  
(here `-j 4` is my assumption, I actually don't know the number of CPUs on the AppVeyor CI test machine; it's not important though)  
  
Note that the command line doesn't set `address-model` and assumes whatever the default is. The problem is that b2 detects default `address-model` to be either "64-bit" or "none", as can be seen in the build log. MSVC versions 9.0 to 11.0 on that system only have compilers targeting 32-bit installed and 12.0 has both 32 and 64-bit. It looks like b2 is unable to detect 32-bit compilers for some reason. The result is that compilation fails because "cl" executable is not found for all compilers except MSVC-12.0 (for which presumably 64-bit compiler is used).  
  
AppVeyor CI config of Boost.Filesystem have not been changed since Jun 5, 2020, and the tests were passing then. I also ran CI of Boost.Atomic, which uses the same compilers on the same AppVeyor CI image, with the difference that that config explicitly specifies `address-model` in all cases (i.e. for MSVC-9.0 it explicitly requests `address-model=32` in the command line). In that build I can also see "default address-model: none", but the build succeeds.  
  
I could not reproduce this locally, but I may have some stale data from previous builds. I remember, b2 used to create auxiliary .cmd files for each MSVC version, but I cannot find them on my system now.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-09-24 00:44:56 UTC  
> Url: https://github.com/boostorg/build/issues/659#issuecomment-698045480  

Also, the build says "default architecture : none", so this is not limited to just `address-model`.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-01-04 00:14:56 UTC  
> Url: https://github.com/boostorg/build/issues/659#issuecomment-753697782  

> I could not reproduce this locally, but I may have some stale data from previous builds.  
  
That could have been because of stale data in `libs/config/architecture/checks/bin`. (https://github.com/boostorg/config/issues/351)

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:31 UTC  
> Url: https://github.com/boostorg/build/issues/659#issuecomment-850859616  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
