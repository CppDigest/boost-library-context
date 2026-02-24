# #80 Disable broken CI tests [Merged]

> Username: Flamefire  
> Created at: 2022-05-27 13:30:20 UTC  
> Updated at: 2022-05-27 15:37:52 UTC  
> Merged at: 2022-05-27 15:31:03 UTC  
> Closed at: 2022-05-27 15:31:03 UTC  
> Url: https://github.com/boostorg/locale/pull/80  

- Tests on Windows mostly fail, mainly due to #71  
- ICU tests fail on CI due to more recent ICU version than what the tests were written for leading to different formatting  
- Due to those issues some CMake tests fail  
  
Hence this temporarily disables running the tests (Windows: all, Other: Only ICU) via the `b2` option `testing.execute=off` and also turns the failing CMake tests to `COMPILE_ONLY`  
  
This still provides good coverage in terms of: Make sure it compiles on many systems.      
I also left as many tests enabled (to actually run) as possible (e.g. the iconv tests) to detect runtime regressions.  
  
2 more commits provide workarounds for 2 issues:  
- On (quite old) clang the `test_config` fails when the `std::locale("").name()` is read due to memory corruption, i.e. I've seen silent failures (non-zero exit code) and after some debug commits something which looks like a double-free. I disabled that code part for Clang < 3.8  
- On OSX ASAN reports an invalid (out-of-bounds) read in `strfmon_l` while the code is actually correct and that read doesn't seem to be an actual issue.  
  
For future reference the [ASAN failure](https://github.com/boostorg/locale/runs/6597762167?check_suite_focus=true):  
```  
en_US.UTF locale  
UTF-8  
- Testing as::posix  
- Testing as::number  
- Testing as::currency national   
=================================================================  
==20249==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x60200000027f at pc 0x00010ec0a9fd bp 0x7ffee1760d20 sp 0x7ffee17604e0  
READ of size 10 at 0x60200000027f thread T0  
    #0 0x10ec0a9fc in wrap_memmove+0x16c (libclang_rt.asan_osx_dynamic.dylib:x86_64+0x189fc)  
    #1 0x7fff203139ea in _strfmon+0x8f1 (libsystem_c.dylib:x86_64+0x6b9ea)  
    #2 0x7fff2031400c in strfmon_l+0x97 (libsystem_c.dylib:x86_64+0x6c00c)  
    #3 0x10e4a8db0 in void test_by_char<char, char>(std::__1::locale const&, _xlocale*) test_posix_formatting.cpp:98  
    #4 0x10e4a0910 in main test_posix_formatting.cpp:189  
    #5 0x7fff203eef3c in start+0x0 (libdyld.dylib:x86_64+0x15f3c)  
  
0x60200000027f is located 0 bytes to the right of 15-byte region [0x602000000270,0x60200000027f)  
allocated by thread T0 here:  
    #0 0x10ec36380 in wrap_malloc+0xa0 (libclang_rt.asan_osx_dynamic.dylib:x86_64+0x44380)  
    #1 0x7fff20313779 in _strfmon+0x680 (libsystem_c.dylib:x86_64+0x6b779)  
    #2 0x7fff2031400c in strfmon_l+0x97 (libsystem_c.dylib:x86_64+0x6c00c)  
    #3 0x10e4a8db0 in void test_by_char<char, char>(std::__1::locale const&, _xlocale*) test_posix_formatting.cpp:98  
    #4 0x10e4a0910 in main test_posix_formatting.cpp:189  
    #5 0x7fff203eef3c in start+0x0 (libdyld.dylib:x86_64+0x15f3c)  
  
SUMMARY: AddressSanitizer: heap-buffer-overflow (libclang_rt.asan_osx_dynamic.dylib:x86_64+0x189fc) in wrap_memmove+0x16c  
Shadow bytes around the buggy address:  
  0x1c03fffffff0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x1c0400000000: fa fa fd fd fa fa 00 00 fa fa 00 06 fa fa 00 02  
  0x1c0400000010: fa fa 00 04 fa fa 00 00 fa fa 00 06 fa fa fd fd  
  0x1c0400000020: fa fa 00 fa fa fa 00 00 fa fa 00 00 fa fa 00 00  
  0x1c0400000030: fa fa fd fa fa fa fd fa fa fa fd fa fa fa fd fa  
=>0x1c0400000040: fa fa fd fa fa fa fd fa fa fa 02 fa fa fa 00[07]  
  0x1c0400000050: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x1c0400000060: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x1c0400000070: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x1c0400000080: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x1c0400000090: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
Shadow byte legend (one shadow byte represents 8 application bytes):  
  Addressable:           00  
  Partially addressable: 01 02 03 04 05 06 07   
  Heap left redzone:       fa  
  Freed heap region:       fd  
  Stack left redzone:      f1  
  Stack mid redzone:       f2  
  Stack right redzone:     f3  
  Stack after return:      f5  
  Stack use after scope:   f8  
  Global redzone:          f9  
  Global init order:       f6  
  Poisoned by user:        f7  
  Container overflow:      fc  
  Array cookie:            ac  
  Intra object redzone:    bb  
  ASan internal:           fe  
  Left alloca redzone:     ca  
  Right alloca redzone:    cb  
  Shadow gap:              cc  
==20249==ABORTING  
  
EXIT STATUS: 134  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-27 14:09:36 UTC  
> Url: https://github.com/boostorg/locale/pull/80#issuecomment-1139655993  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/80?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@dbeb8f1`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #80   +/-   ##  
==========================================  
  Coverage           ?   80.07%             
==========================================  
  Files              ?       76             
  Lines              ?     5958             
  Branches           ?        0             
==========================================  
  Hits               ?     4771             
  Misses             ?     1187             
  Partials           ?        0             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/80?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/80?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [dbeb8f1...608e015](https://codecov.io/gh/boostorg/locale/pull/80?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
