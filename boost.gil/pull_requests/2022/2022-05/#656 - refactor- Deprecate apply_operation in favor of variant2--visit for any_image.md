# #656 refactor: Deprecate apply_operation in favor of variant2::visit for any_image [Merged]

> Username: marco-langer  
> Created at: 2022-05-01 09:48:54 UTC  
> Updated at: 2022-06-26 11:21:41 UTC  
> Merged at: 2022-06-26 11:21:18 UTC  
> Closed at: 2022-06-26 11:21:19 UTC  
> Url: https://github.com/boostorg/gil/pull/656  

### Description  
  
Closes #482  
  
### Tasklist  
  
- [ ] Add test case(s) - see #693  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-01 09:58:10 UTC  
> Updated_at: 2022-06-26 10:27:32 UTC  
> Url: https://github.com/boostorg/gil/pull/656#issuecomment-1114188421  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/656?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#656](https://codecov.io/gh/boostorg/gil/pull/656?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (85f01f9) into [develop](https://codecov.io/gh/boostorg/gil/commit/151fd9c8a318a4249e244eab959592143422a04d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (151fd9c) will **increase** coverage by `0.37%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #656      +/-   ##  
===========================================  
+ Coverage    80.32%   80.69%   +0.37%       
===========================================  
  Files          117      116       -1       
  Lines         5032     5072      +40       
===========================================  
+ Hits          4042     4093      +51       
+ Misses         990      979      -11       
```

---

## Comment 2

> Username: sdebionne  
> Created_at: 2022-05-02 08:31:07 UTC  
> Url: https://github.com/boostorg/gil/pull/656#issuecomment-1114619050  

I think the idea of #482 was just to mark `apply_operation` as `[[deprecated]]` to encourage users to use `visit` directly in user code. I am not sure removing `apply_operation` from the codebase is a good idea, especially if we switch to C++17 in the near future -then `std::variant` will be used.  
  
Thanks for the tests of the dynamic algorithms!

---

## Comment 3

> Username: marco-langer  
> Created_at: 2022-05-02 12:54:08 UTC  
> Url: https://github.com/boostorg/gil/pull/656#issuecomment-1114814272  

Thank you for the feedback!  
  
I might be wrong, but I think we need to do both at the same time: mark `apply_operation()` as deprecated and remove all internal use cases at the same time. Otherwise the deprecated warning will be emitted even if the caller never invokes `apply_operation()` himself. From the compilers point of view it is irrelevant, whether the deprecated function will be called from within the library or from client code.  
  
Please let me know if this assumption is not correct though.

---

## Comment 4

> Username: sdebionne  
> Created_at: 2022-05-02 15:30:24 UTC  
> Url: https://github.com/boostorg/gil/pull/656#issuecomment-1115037721  

> Otherwise the deprecated warning will be emitted even if the caller never invokes apply_operation() himself.  
  
Good point!

---

## Comment 5

> Username: mloskot  
> Created_at: 2022-05-20 09:14:30 UTC  
> Url: https://github.com/boostorg/gil/pull/656#issuecomment-1132670542  

@sdebionne   
> if we switch to C++17 in the near future -then `std::variant` will be used.  
  
Let's do it. I have moved the planning towards C++14/17 to discussion here https://github.com/boostorg/gil/discussions/676

---

## Comment 6

> Username: marco-langer  
> Created_at: 2022-06-04 05:51:06 UTC  
> Url: https://github.com/boostorg/gil/pull/656#issuecomment-1146543725  

With the announcement to change to std::variant with C++17, starting from Boost 1.82, as I understood the [discussion](https://github.com/boostorg/gil/discussions/676), shall we still take this intermediate step here and switch to boost::variant2::visit during for Boost 1.80 and 1.81, or should this issue be postponed to 1.82?

---

## Comment 7

> Username: mloskot  
> Created_at: 2022-06-06 14:09:47 UTC  
> Url: https://github.com/boostorg/gil/pull/656#issuecomment-1147490103  

@marco-langer I don't see any objections to the intermediate step.

---

## Comment 8

> Username: marco-langer  
> Created_at: 2022-06-26 06:40:34 UTC  
> Url: https://github.com/boostorg/gil/pull/656#issuecomment-1166430775  

@mloskot This PR is ready for review. I kept the WIP tag in the last weeks to complete/refactor the tests, but the core library refactoring is done. I think we can add/refactor the tests also after the Boost 1.80 deadline.

---

## Review 9 [Approved]

> Username: mloskot  
> Created_at: 2022-06-26 09:36:33 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/656#pullrequestreview-1019398390  

Awesome work!  
  
The two CI failures on Windows are due to object size, so it's about configuration issue and we can ignore it for now:  
  
```  
D:\a\gil\boost-root\libs\gil\test\extension\dynamic_image\algorithm\copy_and_convert_pixels.cpp:  
   fatal error C1128: number of sections exceeded object file format limit: compile with /bigobj  
    call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo "libs\gil\test\extension\dynamic_image\algorithm\copy_and_convert_pixels.cpp" -c   
    -Fo"bin.v2\libs\gil\test\extension\dynamic_image\algorithm\copy_and_convert_pixels.test\msvc-14.3\dbg\cxstd-14-iso\thrd-mlt\copy_and_convert_pixels.obj"  
    -TP /wd4675 /EHs /std:c++14 /GR /Zc:throwingNew /Z7 /Od /Ob0 /W3 /MDd /Zc:forScope /Zc:wchar_t /Zc:inline /favor:blend -DBOOST_ALL_NO_LIB=1 -D_SILENCE_EXPERIMENTAL_FILESYSTEM_DEPRECATION_WARNING=1 "-I." "-Ilibs\gil\test"   
...failed compile-c-c++ bin.v2\libs\gil\test\extension\dynamic_image\algorithm\copy_and_convert_pixels.test\msvc-14.3\dbg\cxstd-14-iso\thrd-mlt\copy_and_convert_pixels.obj...  
  
  
D:\a\gil\boost-root\libs\gil\test\extension\dynamic_image\algorithm\copy_and_convert_pixels.cpp:  
  fatal error C1128: number of sections exceeded object file format limit: compile with /bigobj  
    call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo "libs\gil\test\extension\dynamic_image\algorithm\copy_and_convert_pixels.cpp" -c  
    -Fo"bin.v2\libs\gil\test\extension\dynamic_image\algorithm\copy_and_convert_pixels.test\msvc-14.3\dbg\cxstd-17-iso\thrd-mlt\copy_and_convert_pixels.obj"  
    -TP /wd4675 /EHs /std:c++17 /GR /Zc:throwingNew /Z7 /Od /Ob0 /W3 /MDd /Zc:forScope /Zc:wchar_t /Zc:inline /favor:blend -DBOOST_ALL_NO_LIB=1 "-I." "-Ilibs\gil\test"   
...failed compile-c-c++ bin.v2\libs\gil\test\extension\dynamic_image\algorithm\copy_and_convert_pixels.test\msvc-14.3\dbg\cxstd-17-iso\thrd-mlt\copy_and_convert_pixels.obj...  
```  
  
I will stick this to `Jamfile`  
  
```  
<toolset>msvc:<cxxflags>/bigobj  
```

---
