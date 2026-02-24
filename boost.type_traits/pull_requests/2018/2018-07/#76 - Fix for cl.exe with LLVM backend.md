# #76 Fix for cl.exe with LLVM backend [Closed]

> Username: matbech  
> Created at: 2018-07-09 10:55:26 UTC  
> Updated at: 2018-10-18 14:23:31 UTC  
> Closed at: 2018-08-06 12:14:21 UTC  
> Url: https://github.com/boostorg/type_traits/pull/76  

When compiling with LLVM 6's cl.exe frontend (\LLVM\msbuild-bin\cl.exe) using LLVM's backend on Windows, the following compiler error is emitted:  
boost\boost/type_traits/has_trivial_move_assign.hpp(49): error : no template named 'is_assignable'; did you mean 'std::is_assignable'?  
  
The reason is that <boost/type_traits/is_assignable.hpp> is not included, because \_\_clang is not defined.  
  
The proposed PR ensures that is_assignable.hpp is included in this situation.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-08-06 12:14:21 UTC  
> Url: https://github.com/boostorg/type_traits/pull/76#issuecomment-410687615  

Thanks, just fixed this as it happens.

---

## Comment 2

> Username: macdew  
> Created_at: 2018-10-18 12:04:01 UTC  
> Url: https://github.com/boostorg/type_traits/pull/76#issuecomment-430982518  

I'm having this issue in boost 1.68.0 - was this fix included in that release?

---

## Comment 3

> Username: MarcelRaad  
> Created_at: 2018-10-18 12:11:14 UTC  
> Url: https://github.com/boostorg/type_traits/pull/76#issuecomment-430984538  

@macdew No: https://github.com/boostorg/type_traits/commit/ad326841ecca3e1a31102d5ddaf4e82f55a13742#diff-6f32db8e7667635f00953149c5f40853

---

## Comment 4

> Username: macdew  
> Created_at: 2018-10-18 14:23:31 UTC  
> Url: https://github.com/boostorg/type_traits/pull/76#issuecomment-431028824  

Thanks Marcel!  I applied the patch to our copy and confirmed that build now completes.

---
