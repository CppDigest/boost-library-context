# #31 Fix compiling problems under ClangC2. [Merged]

> Username: gongminmin  
> Created at: 2017-02-05 22:46:00 UTC  
> Updated at: 2017-02-08 05:23:28 UTC  
> Merged at: 2017-02-06 13:26:25 UTC  
> Closed at: 2017-02-06 13:26:25 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/31  

Clang/C2 in VS2015 defines __c_atomic__, but can't really use it. Disable __c_atomic__ and BOOST_SP_HAS_SYNC for Clang/C2 to fix 2 compiler internal errors.

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-02-05 23:00:05 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/31#issuecomment-277557437  

`defined(_MSC_VER)` will also catch `clang-cl.exe`, the non-C2 one. Are we sure we want to disable these there as well? I haven't received reports of it having problems.

---

## Comment 2

> Username: gongminmin  
> Created_at: 2017-02-05 23:19:31 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/31#issuecomment-277558690  

I'm using VS2015's v140_clang_c2 toolset, and those internal errors happen when using Boost.Signals2. I will investigate more about this. And maybe file a bug against Clang/C2. Thanks.

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-02-05 23:21:04 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/31#issuecomment-277558818  

I think that Clang/C2 defines the `__c2__` macro, which we can use instead.

---

## Comment 4

> Username: gongminmin  
> Created_at: 2017-02-05 23:45:08 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/31#issuecomment-277560271  

Great, the __c2__ works. After some tests, I'll update my patch. Thanks.

---

## Comment 5

> Username: gongminmin  
> Created_at: 2017-02-05 23:52:26 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/31#issuecomment-277560651  

Updated. Now __c2__ is used.

---
