# #128 Fixing buffer overflow in do_length [Merged]

> Username: kevcadieux  
> Created at: 2021-03-14 20:09:45 UTC  
> Updated at: 2022-11-02 03:12:04 UTC  
> Merged at: 2022-11-02 03:12:04 UTC  
> Closed at: 2022-11-02 03:12:04 UTC  
> Url: https://github.com/boostorg/iostreams/pull/128  

This change fixes a buffer overflow in the UTF-8 codecvt facet's `do_length` function that was detected by MSVC's AddressSanitizer when running tests internally. Prior to this change, the character pointed to by `from_end`, which may point to invalid memory, was being dereferenced. The fix is to modify the loop to only dereference a character when we know we haven't yet reached `from_end`.

---

## Comment 1

> Username: mclow  
> Created_at: 2021-03-14 20:13:51 UTC  
> Url: https://github.com/boostorg/iostreams/pull/128#issuecomment-798971950  

is there a test case that exercises this big?

---

## Comment 2

> Username: kevcadieux  
> Created_at: 2021-03-14 20:24:22 UTC  
> Url: https://github.com/boostorg/iostreams/pull/128#issuecomment-798973495  

> is there a test case that exercises this big?  
  
Hello Marshall, the bug was initially discovered by running the tests in the detail library with ASan turned on (i.e. `.\b2 libs\detail\test`). The specific test within that suite that generated the error is `test_utf8_codecvt`, resulting in the error output shown below. I [submitted a PR in the detail repo](https://github.com/boostorg/detail/pull/21), but I noticed that `iostreams` uses the same algorithm for `do_length`  as the `detail` library, so I opened a GitHub PR for `iostreams` as well.  
  
```  
==12552==ERROR: AddressSanitizer: global-buffer-overflow on address 0x00afb68c at pc 0x00aea0ba bp 0x010fed7c sp 0x010fed70  
READ of size 1 at 0x00afb68c thread T0  
    #0 0xaea0b9 in boost::detail::utf8_codecvt_facet::do_length(struct _Mbstatet &,char const *,char const *,unsigned int)const  F:\gitP\boostorg\boost\boost\detail\utf8_codecvt_facet.ipp:207  
    #1 0xaedd03 in test_main(int,char * * const) F:\gitP\boostorg\boost\libs\detail\test\test_utf8_codecvt.cpp:261  
    #2 0xaef509 in main F:\gitP\boostorg\boost\libs\detail\test\test_utf8_codecvt.cpp:285  
    #3 0xaf0232 in _scrt_common_main_seh D:\a01\_work\14\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:288  
    #4 0x755462c3  (C:\windows\System32\KERNEL32.DLL+0x6b8162c3)  
    #5 0x77050778  (C:\windows\SYSTEM32\ntdll.dll+0x4b2e0778)  
    #6 0x77050743  (C:\windows\SYSTEM32\ntdll.dll+0x4b2e0743)  
```

---

## Comment 3

> Username: Lastique  
> Created_at: 2021-03-17 11:26:41 UTC  
> Url: https://github.com/boostorg/iostreams/pull/128#issuecomment-801006715  

I've committed a different version of this fix in https://github.com/boostorg/detail/commit/131208d8ccd82ef69afb9cf0bad1a314bd931d88, which also fixes incrementing `from` past `from_end` that this code does. I'd suggest using the code from my commit as the base for this fix.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2021-03-18 04:37:03 UTC  
> Updated_at: 2021-03-18 04:39:38 UTC  
> Url: https://github.com/boostorg/iostreams/pull/128#issuecomment-801617353  

# [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/128?src=pr&el=h1) Report  
> Merging [#128](https://codecov.io/gh/boostorg/iostreams/pull/128?src=pr&el=desc) (cb31ced) into [develop](https://codecov.io/gh/boostorg/iostreams/commit/1a260555d51880273d0e3a3ac6782c36f3c131ef?el=desc) (1a26055) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/iostreams/pull/128/graphs/tree.svg?width=650&height=150&src=pr&token=LBEfwtNfca)](https://codecov.io/gh/boostorg/iostreams/pull/128?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #128   +/-   ##  
========================================  
  Coverage    68.84%   68.84%             
========================================  
  Files           80       80             
  Lines         3444     3444             
  Branches      1027     1027             
========================================  
  Hits          2371     2371             
  Misses         454      454             
  Partials       619      619             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/iostreams/pull/128?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [.../iostreams/detail/streambuf/indirect\_streambuf.hpp](https://codecov.io/gh/boostorg/iostreams/pull/128/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZGV0YWlsL3N0cmVhbWJ1Zi9pbmRpcmVjdF9zdHJlYW1idWYuaHBw) | `75.30% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/iostreams/pull/128?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/128?src=pr&el=footer). Last update [1a26055...cb31ced](https://codecov.io/gh/boostorg/iostreams/pull/128?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 5

> Username: kevcadieux  
> Created_at: 2021-04-30 21:18:45 UTC  
> Updated_at: 2021-04-30 21:18:58 UTC  
> Url: https://github.com/boostorg/iostreams/pull/128#issuecomment-830403748  

@mclow I thought the comment from Codecov meant that this fix had been merged but I'm not seeing it in `test/detail/utf8_codecvt_facet.cpp`. Do you know what happened?

---

## Comment 6

> Username: mclow  
> Created_at: 2022-11-02 03:11:51 UTC  
> Url: https://github.com/boostorg/iostreams/pull/128#issuecomment-1299505239  

> @mclow I thought the comment from Codecov meant that this fix had been merged but I'm not seeing it in `test/detail/utf8_codecvt_facet.cpp`. Do you know what happened?  
  
@Lastique fixed the problem in `include/boost/detail/utf8_codecvt_facet.ipp`, and your patch makes a similar (identical?) change in `test/detail/utf8_codecvt_facet.cpp`.

---
