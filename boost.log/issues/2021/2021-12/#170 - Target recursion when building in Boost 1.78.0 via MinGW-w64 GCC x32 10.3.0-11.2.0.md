# #170 - Target recursion when building in Boost 1.78.0 via MinGW-w64 GCC x32 10.3.0/11.2.0 [Closed]

> Username: mamontov-cpp  
> Created at: 2021-12-09 19:22:24 UTC  
> Updated at: 2021-12-12 18:00:30 UTC  
> Closed at: 2021-12-09 22:16:37 UTC  
> Url: https://github.com/boostorg/log/issues/170  

Moved from https://github.com/bfgroup/b2/issues/112  
  
When trying to compile the library on Windows 10 host with aforementioned compiler settings I stumbled upon the issue, that it generates recursion in targets. See the log part below (full logs are in original issue)  
  
```  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:609: in start-building from module targets  
error: Recursion in main target references  
error: the following target are being built currently:  
error: ./forward ./stage ./stage-proper libs/filesystem/build/stage libs/filesystem/build/stage-dependencies libs/log/build/stage libs/log/build/stage-dependencies libs/filesystem/build/stage  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:776: in class@main-target.generate from module object(main-target)@8379  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/boost_install\boost-install.jam:1171: in generate-dependencies from module boost-install  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/kernel\modules.jam:106: in modules.call-in from module boost-install  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/util\indirect.jam:105: in indirect.call from module indirect  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/tools\generate.jam:59: in construct from module object(generated-target-class)@3793  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:1393: in class@basic-target.generate from module object(generated-target-class)@3793  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:812: in generate-really from module object(main-target)@10865  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:784: in class@main-target.generate from module object(main-target)@10865  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:934: in targets.generate-from-reference from module targets  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:1277: in generate-dependencies from module object(alias-target-class)@3794  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:1351: in class@basic-target.generate from module object(alias-target-class)@3794  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:812: in generate-really from module object(main-target)@10866  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:784: in class@main-target.generate from module object(main-target)@10866  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/boost_install\boost-install.jam:1171: in generate-dependencies from module boost-install  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/kernel\modules.jam:106: in modules.call-in from module boost-install  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/util\indirect.jam:105: in indirect.call from module indirect  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/tools\generate.jam:59: in construct from module object(generated-target-class)@2753  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:1393: in class@basic-target.generate from module object(generated-target-class)@2753  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:812: in generate-really from module object(main-target)@8378  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:784: in class@main-target.generate from module object(main-target)@8378  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:934: in targets.generate-from-reference from module targets  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:1277: in generate-dependencies from module object(alias-target-class)@2754  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:1351: in class@basic-target.generate from module object(alias-target-class)@2754  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:812: in generate-really from module object(main-target)@8379  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:784: in class@main-target.generate from module object(main-target)@8379  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:934: in targets.generate-from-reference from module targets  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:1277: in generate-dependencies from module object(alias-target-class)@184  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:1351: in class@basic-target.generate from module object(alias-target-class)@184  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:812: in generate-really from module object(main-target)@6581  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:784: in class@main-target.generate from module object(main-target)@6581  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:934: in targets.generate-from-reference from module targets  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:1277: in generate-dependencies from module object(top-level-target)@190  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:1351: in alias-target-class.generate from module object(top-level-target)@190  
D:/Projects/saddy-related/w32/boost_1_78_0\boostcpp.jam:430: in build-multiple from module object(top-level-target)@190  
D:/Projects/saddy-related/w32/boost_1_78_0\boostcpp.jam:420: in class@top-level-target.generate from module object(top-level-target)@190  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:812: in generate-really from module object(main-target)@6583  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:784: in class@main-target.generate from module object(main-target)@6583  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:934: in targets.generate-from-reference from module targets  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:1277: in generate-dependencies from module object(top-level-target)@191  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:1351: in alias-target-class.generate from module object(top-level-target)@191  
D:/Projects/saddy-related/w32/boost_1_78_0\boostcpp.jam:430: in build-multiple from module object(top-level-target)@191  
D:/Projects/saddy-related/w32/boost_1_78_0\boostcpp.jam:420: in class@top-level-target.generate from module object(top-level-target)@191  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:812: in generate-really from module object(main-target)@6584  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:784: in class@main-target.generate from module object(main-target)@6584  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/build\targets.jam:273: in class@project-target.generate from module object(project-target)@136  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src\build-system.jam:805: in load from module build-system  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/kernel\modules.jam:294: in import from module modules  
D:/Projects/saddy-related/w32/boost_1_78_0/tools/build/src/kernel/bootstrap.jam:139: in boost-build from module  
D:/Projects/saddy-related/w32/boost_1_78_0/boost-build.jam:17: in module scope from module  
```  
  
I've tried to debug it in original issue but it looks like the recursion is somehow related to target libs/log/build/stage libs/log/build/stage-dependencies . I don't have much experience with Jamfiles sadly - but it may be related to inner checks for log?   
  
Note, that the issue is not occuring when using GCC x64.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-12-09 22:16:37 UTC  
> Updated at: 2021-12-09 22:17:07 UTC  
> Url: https://github.com/boostorg/log/issues/170#issuecomment-990339920  

Builds fine for me on [MinGW-w64](https://sourceforge.net/projects/mingw-w64/), both 32 and 64-bit binaries. Also, builds fine in [CI](https://ci.appveyor.com/project/Lastique/log/builds/41814621). Note that the latest gcc version in this build is 8.1.0.  
  
I was able to reproduce this in [MSYS2](https://www.msys2.org/) build of MinGW-w64, but I don't think we ever supported that compiler. It was never tested, to my knowledge.  
  
I was able to work around this error by explicitly specifying `address-model=32` for the 32-bit build. This is needed anyway because the compiler is unable to produce 64-bit binaries, and Boost.Build requests 64-bit binaries on 64-bit host Windows by default. You will need to specify `address-model=64` when you build with a 64-bit compiler for the same reason, `address-model=32` will not work. The same applies to the original MinGW-w64, BTW.  
  
I'm not sure why Boost.Build fails with the error with MSYS2 and not MinGW-w64. I suspect, this may have something to do with how Boost.Build deduces default `address-model` on Windows. In any case, I don't see what I can do about it in Boost.Log. Please, report this to Boost.Build.

---

## Comment 2

> Username: mamontov-cpp  
> Created at: 2021-12-09 22:30:25 UTC  
> Url: https://github.com/boostorg/log/issues/170#issuecomment-990354303  

Wow, workaround really worked for me. Thank you!

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-12-09 23:18:31 UTC  
> Url: https://github.com/boostorg/log/issues/170#issuecomment-990392583  

> Boost.Build requests 64-bit binaries on 64-bit host Windows by default. You will need to specify `address-model=64` when you build with a 64-bit compiler for the same reason, `address-model=32` will not work. The same applies to the original MinGW-w64, BTW.  
>   
> I'm not sure why Boost.Build fails with the error with MSYS2 and not MinGW-w64. I suspect, this may have something to do with how Boost.Build deduces default `address-model` on Windows. In any case, I don't see what I can do about it in Boost.Log. Please, report this to Boost.Build.  
  
B2 does **not** deduce `address-model` (or `architecture`), it's an optional feature, so it doesn't exist when a user have not requested a particular one. I saw some libraries and IIRC even the root boost script trying to deduce values and getting it wrong, Log seems to be one of them  
https://github.com/boostorg/log/blob/a3717b0ccf038724ca4ece79da49844139360bf7/build/log-arch-config.jam#L22-L72  
  
That's said I still don't understand how filesystem -> log -> filesystem cycles is formed (`./forward -> ./stage -> ./stage-proper -> ***libs/filesystem/build/stage*** -> libs/filesystem/build/stage-dependencies -> libs/log/build/stage -> libs/log/build/stage-dependencies -> ***libs/filesystem/build/stage***`)

---

## Comment 4

> Username: Lastique  
> Created at: 2021-12-09 23:32:56 UTC  
> Url: https://github.com/boostorg/log/issues/170#issuecomment-990400054  

> I saw some libraries and IIRC even the root boost script trying to deduce values and getting it wrong, Log seems to be one of them  
  
To clarify, the "wrong" part was not because Boost.Log (or other libraries) did something wrong. The code you linked is perfectly valid, as far as I understand, and the issues were caused by a problem in Boost.Config. See https://github.com/boostorg/build/issues/659 and https://github.com/boostorg/config/issues/351. That should be fixed by now, though.  
  
> That's said I still don't understand how filesystem -> log -> filesystem cycles is formed  
  
...and also why `address-model` resolves it. Which is why I suspect it might be related.

---

## Comment 5

> Username: Kojoley  
> Created at: 2021-12-09 23:45:48 UTC  
> Url: https://github.com/boostorg/log/issues/170#issuecomment-990408599  

https://github.com/boostorg/build/issues/659 has nothing to do with GCC, it is about MSVC toolset which IIRC does not support compilers located in Windows SDK.

---

## Comment 6

> Username: Lastique  
> Created at: 2021-12-10 00:42:19 UTC  
> Url: https://github.com/boostorg/log/issues/170#issuecomment-990456507  

That problem was not specific to MSVC.

---

## Comment 7

> Username: Kojoley  
> Created at: 2021-12-10 00:49:03 UTC  
> Url: https://github.com/boostorg/log/issues/170#issuecomment-990460872  

> That problem was not specific to MSVC.  
  
Are you sure we are talking about the same thing?

---

## Comment 8

> Username: Kojoley  
> Created at: 2021-12-10 00:51:54 UTC  
> Url: https://github.com/boostorg/log/issues/170#issuecomment-990462849  

What about the original issue, it is rises from  
```jam  
lib advapi32 ;  
  
project  
    : requirements  
      <library>advapi32  
    ;  
```  
I don't understand why the cycle is detected when both log and filesystem is doing that, B2 should detect it even in example above.

---

## Comment 9

> Username: Lastique  
> Created at: 2021-12-10 01:30:50 UTC  
> Url: https://github.com/boostorg/log/issues/170#issuecomment-990500030  

> Are you sure we are talking about the same thing?  
  
I was talking about the issues I linked in https://github.com/boostorg/log/issues/170#issuecomment-990400054. If you look at the Boost.Config issue and the related PR, it is clear that the problem is not specific to MSVC.  
  
> I don't understand why the cycle is detected when both log and filesystem is doing that, B2 should detect it even in example above.  
  
No idea. But this should be an issue for Boost.Build.

---

## Comment 10

> Username: Kojoley  
> Created at: 2021-12-10 01:46:38 UTC  
> Url: https://github.com/boostorg/log/issues/170#issuecomment-990518186  

Meh, at times I think why I am caring about the problem that does not affecting me in any way, I debugged for you the issue in libraries you are maintaining and you show zero respect. Best wishes.

---

## Comment 11

> Username: mamontov-cpp  
> Created at: 2021-12-10 08:34:40 UTC  
> Updated at: 2021-12-10 08:35:05 UTC  
> Url: https://github.com/boostorg/log/issues/170#issuecomment-990735468  

Wow, didn't know, that this caused such discussion.  Also, if address-model is not specified GCC x32 tries to build x64 builds. It fails at it, but that may be the issue, when platform-dependent checks are performed - what if something goes off there?   
Should it be also added to Boost.Config issues?

---

## Comment 12

> Username: Lastique  
> Created at: 2021-12-10 10:50:44 UTC  
> Url: https://github.com/boostorg/log/issues/170#issuecomment-990860707  

> Meh, at times I think why I am caring about the problem that does not affecting me in any way, I debugged for you the issue in libraries you are maintaining and you show zero respect. Best wishes.  
  
Not sure where that's coming from. I certainly did not mean to offend you. I was merely pointing out that this is not the place to discuss Boost.Build issues. Boost.Build maintainers will not be monitoring this issue, so any debugging you've made would go unnoticed.  
  
> It fails at it, but that may be the issue, when platform-dependent checks are performed - what if something goes off there?  
Should it be also added to Boost.Config issues?  
  
I think, the recursion error is a Boost.Build issue, not Boost.Config. At the very least, Boost.Build maintainers need to have a look at the problem and tell if there is something wrong with what the libraries are doing.  
  
The fact that it tries to build both 32 and 64-bit binaries is probably a separate issue, also in Boost.Build, IMO. Despite what @Kojoley says about `address-model` (which is correct), it is Boost.Build that decides to build 32-bit or 64-bit or both kinds of binaries based on its defaults and user's requests.

---

## Comment 13

> Username: mamontov-cpp  
> Created at: 2021-12-10 13:08:48 UTC  
> Url: https://github.com/boostorg/log/issues/170#issuecomment-990960012  

>It is Boost.Build that decides to build 32-bit or 64-bit or both kinds of binaries based on its defaults and user's requests.  
  
@Kojoley  Is it though?

---

## Comment 14

> Username: Kojoley  
> Created at: 2021-12-10 15:23:05 UTC  
> Url: https://github.com/boostorg/log/issues/170#issuecomment-991061374  

> I was merely pointing out that this is not the place to discuss Boost.Build issues  
  
Project requirements applying to the libraries in the project is not an B2 issue, that's the whole point of the project requirements feature. Of course, an reliable dependency cycles diagnostic would be preferable, but it is a missing feature, not a bug.  
  
> if address-model is not specified GCC x32 tries to build x64 builds  
  
For me `b2 toolset=gcc myproj` builds 32bit executables.  
  
> > It is Boost.Build that decides to build 32-bit or 64-bit or both kinds of binaries based on its defaults and user's requests.  
>   
> @Kojoley Is it though?  
  
`--build-type=complete` which, I suppose, makes that does not belong to Boost.Build (B2), it's in https://github.com/boostorg/boost/blob/master/boostcpp.jam

---

## Comment 15

> Username: mamontov-cpp  
> Created at: 2021-12-12 18:00:30 UTC  
> Url: https://github.com/boostorg/log/issues/170#issuecomment-991942971  

@Kojoley  Ok, thank you.
