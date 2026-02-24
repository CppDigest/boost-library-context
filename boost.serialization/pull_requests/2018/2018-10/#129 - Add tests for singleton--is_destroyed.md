# #129 Add tests for singleton::is_destroyed [Closed]

> Username: Flamefire  
> Created at: 2018-10-17 07:55:21 UTC  
> Updated at: 2024-12-09 10:29:16 UTC  
> Closed at: 2018-11-17 19:31:00 UTC  
> Url: https://github.com/boostorg/serialization/pull/129  

This is basically the same test as in #110.  
  
However in #105 @pdimov noted, that it might be easier, to duplicate some test code and split it into 2 separate test cases (https://github.com/boostorg/serialization/pull/105#discussion_r225581561), so this is done here.  
  
As mentioned in the [docu](https://www.boost.org/doc/libs/1_67_0/libs/serialization/doc/singleton.html) the singleton may be used in 2 cases and "Both are used in the serialization library. ":  
  
- plain: simple class used as singleton<Foo>  
- inherited: Foo inherits from singleton<Foo> and can be used directly  
  
Furthermore there is `is_destroyed` which shall "Return true if the destructor on this singleton has been called. Otherwise, return false."  
  
This behaviour is never tested which is the reason for this PR.  
  
Assumption: Classes are destructed in the reverse order of the return from their constructor (see C++ standard) and static function objects are destructed at program termination. This is the base for the `controller` class:  
  
- Accessed from the singleton under test `Foo` in its constructor -> `controller` is destructed after `Foo`  
- `Foo`s ctor AND dtor will set a variable in the `controller` to the current state  
- This state can be used in the dtor of `controller` to check that the singleton was destroyed (if it wasn't there would be a memory leak)  
- The controller can also check `is_destroyed` of the singleton which must be `true` due to the former  
- The main function checks the `is_destroyed` returns `false` and accesses any member of `Foo` to avoid its construction being eliminated.  
  
As explained multiple times `is_destroyed` does not return `true` as it should for the plain singleton. #105 contains the fix for this (will rebase #105 when this is merged)

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2018-10-17 14:22:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/129#pullrequestreview-165657806  

📁 test/test_singleton_inherited.cpp

```diff
   1 |+ /////////1/////////2/////////3/////////4/////////5/////////6/////////7/////////8
   2 |+ // test_singleton_plain.cpp:
```

> Username: pdimov  
> Created_at: 2018-10-17 14:22:29 UTC  
> Updated_at: 2018-11-07 08:19:20 UTC  
> Url: https://github.com/boostorg/serialization/pull/129#discussion_r225951008  

Should be "_inherited".

> Username: Flamefire  
> Created_at: 2018-10-17 14:24:20 UTC  
> Updated_at: 2018-11-07 08:19:20 UTC  
> Url: https://github.com/boostorg/serialization/pull/129#discussion_r225951794  

Ouch. Shall I remove the file name entirely? I see no use for it...

> Username: pdimov  
> Created_at: 2018-10-17 14:24:56 UTC  
> Updated_at: 2018-11-07 08:19:20 UTC  
> Url: https://github.com/boostorg/serialization/pull/129#discussion_r225952097  

Neither do I. :-)


---

## Comment 2

> Username: pdimov  
> Created_at: 2018-10-17 14:24:34 UTC  
> Url: https://github.com/boostorg/serialization/pull/129#issuecomment-430649431  

Looks good to me. Perhaps you could now rework the fix in #105 to not revert Robert's subsequent changes to singleton.hpp? Just the minimum patch necessary to fix this.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2018-10-18 10:13:01 UTC  
> Url: https://github.com/boostorg/serialization/pull/129#issuecomment-430953898  

Good idea: #131 is that

---

## Comment 4

> Username: Flamefire  
> Created_at: 2018-11-07 11:37:03 UTC  
> Url: https://github.com/boostorg/serialization/pull/129#issuecomment-436595688  

Rebased on develop. Still shows the bug as before:  
  
> what():  libs/serialization/test/test_singleton_plain.cpp(66) Assertion failed: boost::serialization::singleton<Foo>::is_destroyed()

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-11-07 21:22:00 UTC  
> Url: https://github.com/boostorg/serialization/pull/129#issuecomment-436781826  

The existing CI builds failed:  
```  
testing.capture-output ..\..\..\bin.v2\libs\serialization\test\test_singleton_plain.test\gcc-6.3.0\debug\link-static\visibility-hidden\test_singleton_plain.run  
====== BEGIN OUTPUT ======  
No errors detected.  
This application has requested the Runtime to terminate it in an unusual way.  
Please contact the application's support team for more information.  
terminate called after throwing an instance of 'std::runtime_error'  
  what():  test_singleton_plain.cpp(66) Assertion failed: boost::serialization::singleton<Foo>::is_destroyed()  
   
EXIT STATUS: 3   
====== END OUTPUT ======  
    set Path=C:\mingw-w64\i686-6.3.0-posix-dwarf-rt_v5-rev1\mingw32\bin;C:\mingw-w64\i686-6.3.0-posix-dwarf-rt_v5-rev1\mingw32\lib;C:\mingw-w64\i686-6.3.0-posix-dwarf-rt_v5-rev1\mingw32\lib32;C:\mingw-w64\i686-6.3.0-posix-dwarf-rt_v5-rev1\mingw32\lib64;%Path%  
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\..\..\bin.v2\libs\serialization\test\test_singleton_plain.test\gcc-6.3.0\debug\link-static\visibility-hidden\test_singleton_plain.exe"   > "..\..\..\bin.v2\libs\serialization\test\test_singleton_plain.test\gcc-6.3.0\debug\link-static\visibility-hidden\test_singleton_plain.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "..\..\..\bin.v2\libs\serialization\test\test_singleton_plain.test\gcc-6.3.0\debug\link-static\visibility-hidden\test_singleton_plain.output"  
    echo EXIT STATUS: %status% >> "..\..\..\bin.v2\libs\serialization\test\test_singleton_plain.test\gcc-6.3.0\debug\link-static\visibility-hidden\test_singleton_plain.output"  
    if %status% EQU 0 (  
        copy "..\..\..\bin.v2\libs\serialization\test\test_singleton_plain.test\gcc-6.3.0\debug\link-static\visibility-hidden\test_singleton_plain.output" "..\..\..\bin.v2\libs\serialization\test\test_singleton_plain.test\gcc-6.3.0\debug\link-static\visibility-hidden\test_singleton_plain.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\..\..\bin.v2\libs\serialization\test\test_singleton_plain.test\gcc-6.3.0\debug\link-static\visibility-hidden\test_singleton_plain.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
...failed testing.capture-output ..\..\..\bin.v2\libs\serialization\test\test_singleton_plain.test\gcc-6.3.0\debug\link-static\visibility-hidden\test_singleton_plain.run...  
```

---

## Comment 6

> Username: jeking3  
> Created_at: 2018-11-07 21:35:42 UTC  
> Url: https://github.com/boostorg/serialization/pull/129#issuecomment-436785780  

I think it would be nice to close all the previous pull requests currently outstanding on this issue.  We've got this one showing the issue, and the follow-up.  I'm still concerned about Boost.Serialization and 1.69.0 however, since it looks like Windows builds are broken on develop.

---

## Comment 7

> Username: Flamefire  
> Created_at: 2018-11-08 06:45:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/129#issuecomment-436890387  

> The existing CI builds failed:  
  
This is the error I mentioned and wanted to show in this PR

---

## Comment 8

> Username: robertramey  
> Created_at: 2018-11-17 19:31:00 UTC  
> Url: https://github.com/boostorg/serialization/pull/129#issuecomment-439641706  

I believe this is resolved.  If anyone is unhappy about this, please open a new PR.

---
