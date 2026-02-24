# #159 Fixed paths for vc15 and arch variable [Merged]

> Username: teeks99  
> Created at: 2017-02-02 22:34:06 UTC  
> Updated at: 2021-10-02 22:18:53 UTC  
> Merged at: 2017-02-03 02:47:25 UTC  
> Closed at: 2017-02-03 02:47:25 UTC  
> Url: https://github.com/boostorg/build/pull/159  

I had some problems when my tester with Visual Studio 2017 RC (msvc-15.0) installed pulled the boost build commit that included merge #158.   
  
Specifically, the default install path for msvc-15.0 is `C:\Program Files (x86)\...` so if we are going to use the paths to determine if it is installed (that is super dangerous!) we should use the `%ProgramFiles(x86)%` env variable. When building on a 32-bit system (or from a 32-bit command prompt like you get when python 32-bit calls `os.system('build.bat')`) that variable with simply point to the same location as `%ProgramFiles%`, so I'd argue it is better to use this.  
  
Then there was an issue with the call to `vsvarsall.bat`, which in msvc-15.0 has a required first argument of the architecture. I'm not sure if there is a canonical boost  build way to pick a default architecture...so I start with x86, then use the `%PROCESSOR_ARCHTECTURE%` variable to upgrade to amd64 for most users. However if the user's environment includes the  `%Platform%` variable, it will override the others...as this is what microsoft sets up when they are doing cross-compiling (e.g. amd64 -> x86).

---

## Review 1 [Commented]

> Username: KindDragon  
> Created_at: 2017-02-08 02:08:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/159#pullrequestreview-20669201  

📁 src/engine/build.bat

```diff
 113 | call :Clear_Error
 114 |- if EXIST "%ProgramFiles%\Microsoft Visual Studio\2017\Professional\VC\Auxiliary\Build\vcvarsall.bat"  (
 114 |+ if EXIST "%ProgramFilesi(x86)%\Microsoft Visual Studio\2017\Professional\VC\Auxiliary\Build\vcvarsall.bat"  (
```

> Username: KindDragon  
> Created_at: 2017-02-08 02:08:33 UTC  
> Url: https://github.com/boostorg/build/pull/159#discussion_r99977970  

Why %ProgramFiles**i**(x86)% with i?

> Username: teeks99  
> Created_at: 2017-02-08 13:35:21 UTC  
> Updated_at: 2017-02-08 13:35:22 UTC  
> Url: https://github.com/boostorg/build/pull/159#discussion_r100065653  

That was a typo.

> Username: KindDragon  
> Created_at: 2017-02-08 13:47:18 UTC  
> Url: https://github.com/boostorg/build/pull/159#discussion_r100067791  

Can you send PR to fix it?

> Username: teeks99  
> Created_at: 2017-02-08 22:38:34 UTC  
> Url: https://github.com/boostorg/build/pull/159#discussion_r100189320  

In #161


---

## Review 2 [Commented]

> Username: KindDragon  
> Created_at: 2017-02-08 02:11:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/159#pullrequestreview-20669465  

📁 src/engine/build.bat

```diff
 108 | call :Clear_Error
 109 |- if EXIST "%ProgramFiles%\Microsoft Visual Studio\2017\Enterprise\VC\Auxiliary\Build\vcvarsall.bat"  (
 109 |+ if EXIST "%ProgramFiles(x86)%\Microsoft Visual Studio\2017\Enterprise\VC\Auxiliary\Build\vcvarsall.bat"  (
```

> Username: KindDragon  
> Created_at: 2017-02-08 02:11:00 UTC  
> Updated_at: 2017-02-08 02:11:31 UTC  
> Url: https://github.com/boostorg/build/pull/159#discussion_r99978227  

"%ProgramFiles%" or "%ProgramFiles(x86)% depends on your Windows (32-bit or 64-bit). Same with  VS140COMNTOOLS variable and others

> Username: teeks99  
> Created_at: 2017-02-08 13:39:21 UTC  
> Url: https://github.com/boostorg/build/pull/159#discussion_r100066387  

I had thought that windows 32-bit (Vista and up) defined both %ProgramFiles% and %ProgramFiles(x86)% to be the same C:\Program Files\. Then on 64-bit systems, %ProgramFiles% points to C:\Program Files\ and %ProgramFiles(x86)% points to C:\Program Files (x86)\.  Is that not correct? I haven't had a 32-bit machine for a long time, so I can't easily look.  
  
Regardless, I think searching for a specific path is a bad long-term solution. There isn't any guarantee that a user will install it there (it is selectable during install). If the VS15COMNTOOLS variable isn't reliable, then maybe we can query the registry or something to make it work?   
  
I really just submitted this pull request as an interm fix because it was breaking my regression tests on a machine that happened to have msvc-15 installed on it.

> Username: KindDragon  
> Created_at: 2017-02-08 13:46:43 UTC  
> Url: https://github.com/boostorg/build/pull/159#discussion_r100067709  

> Is that not correct? I haven't had a 32-bit machine for a long time, so I can't easily look.  
  
I don't have it too, but from my memory, old 32-bit Windows had only %ProgramFiles%. But I think it fine for now.  
  
> If the VS15COMNTOOLS variable isn't reliable, then maybe we can query the registry or something to make it work?  
  
It is not easy to do for VS2017 from scripting languages http://stackoverflow.com/q/41106407/61505

> Username: teeks99  
> Created_at: 2017-02-08 22:39:27 UTC  
> Url: https://github.com/boostorg/build/pull/159#discussion_r100189511  

Yuck, hopefully when the RTM is made available in the next couple days they'll have a better solution.


---

## Comment 3

> Username: AndrewPardoe  
> Created_at: 2017-02-15 16:32:58 UTC  
> Url: https://github.com/boostorg/build/pull/159#issuecomment-280061640  

Just a quick note: I'm on the MSVC team and am trying to find an answer for this from the VS folks.

---

## Comment 4

> Username: KindDragon  
> Created_at: 2017-02-15 20:25:35 UTC  
> Url: https://github.com/boostorg/build/pull/159#issuecomment-280128366  

@AndrewPardoe Thank you. If you find out something, please let me know in issue #157

---

## Comment 5

> Username: AndrewPardoe  
> Created_at: 2017-02-16 03:13:17 UTC  
> Url: https://github.com/boostorg/build/pull/159#issuecomment-280218736  

It's ugly, but commenting now.

---
