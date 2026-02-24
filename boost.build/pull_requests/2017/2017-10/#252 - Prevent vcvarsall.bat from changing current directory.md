# #252 Prevent vcvarsall.bat from changing current directory [Closed]

> Username: hia3  
> Created at: 2017-10-09 17:43:03 UTC  
> Updated at: 2021-10-02 22:08:25 UTC  
> Closed at: 2017-11-23 14:11:29 UTC  
> Url: https://github.com/boostorg/build/pull/252  

Building with VS2017 15.3.5 produces following error:  
  
c1: fatal error C1083: Cannot open source file: 'yyacc.c': No such file or directory  
  
This happens because vsdevcmd_end.bat changes current directory to %USERPROFILE%\Source if it exists.

---

## Comment 1

> Username: hia3  
> Created_at: 2017-10-09 17:47:18 UTC  
> Url: https://github.com/boostorg/build/pull/252#issuecomment-335231753  

Here is what vsdevcmd_end.bat does:  
  
    @REM Set the current directory that users will be set after the script completes  
    @REM in the following order:  
    @REM 1. [VSCMD_START_DIR] will be used if specified in the user environment  
    @REM 2. [USERPROFILE]\source if it exists  
    @REM 3. current directory  
    if "%VSCMD_START_DIR%" NEQ "" (  
        cd /d "%VSCMD_START_DIR%"  
    ) else (  
        if EXIST "%USERPROFILE%\Source" (  
            cd /d "%USERPROFILE%\Source"  
        )  
    )

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-11-16 18:34:51 UTC  
> Url: https://github.com/boostorg/build/pull/252#issuecomment-345016142  

Why has this been closed?

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-11-16 18:37:12 UTC  
> Url: https://github.com/boostorg/build/pull/252#issuecomment-345016818  

This problem has just been reported against 1.66 beta 1 RC 1: https://lists.boost.org/Archives/boost/2017/11/239824.php

---

## Comment 4

> Username: hia3  
> Created_at: 2017-11-16 18:44:53 UTC  
> Updated_at: 2017-11-16 18:45:01 UTC  
> Url: https://github.com/boostorg/build/pull/252#issuecomment-345022005  

> Why has this been closed?  
  
I wasn't sure why this patch works. Feel free to apply it.

---

## Comment 5

> Username: hia3  
> Created_at: 2017-11-16 18:48:31 UTC  
> Updated_at: 2017-11-16 18:49:00 UTC  
> Url: https://github.com/boostorg/build/pull/252#issuecomment-345024751  

Simple `pushd .` is enough, no need for `%CD%`

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-11-16 18:52:46 UTC  
> Url: https://github.com/boostorg/build/pull/252#issuecomment-345025945  

Yes, I was going to say the same thing.

---

## Comment 7

> Username: jhunold  
> Created_at: 2017-11-17 06:55:22 UTC  
> Url: https://github.com/boostorg/build/pull/252#issuecomment-345161202  

Created an edited version on #263. Will merge to develop as soon as CI passes.

---

## Comment 8

> Username: grafikrobot  
> Created_at: 2017-11-23 14:11:29 UTC  
> Url: https://github.com/boostorg/build/pull/252#issuecomment-346628375  

Fixed with #263 instead.

---
