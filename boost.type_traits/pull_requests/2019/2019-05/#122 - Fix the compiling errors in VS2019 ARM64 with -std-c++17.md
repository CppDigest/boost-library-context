# #122 Fix the compiling errors in VS2019 ARM64 with /std:c++17 [Merged]

> Username: gongminmin  
> Created at: 2019-05-05 22:22:23 UTC  
> Updated at: 2019-06-14 02:49:42 UTC  
> Merged at: 2019-06-13 10:17:58 UTC  
> Closed at: 2019-06-13 10:17:58 UTC  
> Url: https://github.com/boostorg/type_traits/pull/122  

Skip __stdcall and __fastcall overloads on ARM and ARM64  
  
Fix issue #121

---

## Comment 1

> Username: ThomsonTan  
> Created_at: 2019-06-06 23:37:55 UTC  
> Url: https://github.com/boostorg/type_traits/pull/122#issuecomment-499706656  

> #endif // _MSC_VER  
  
Update the comment here according to the fix?  
  
---  
Refers to: include/boost/type_traits/detail/is_function_cxx_11.hpp:562 in ccbe9f7. [](commit_id = ccbe9f7abe799ef667760c6a59f95bfd5927e901, deletion_comment = False)

---

## Comment 2

> Username: ThomsonTan  
> Created_at: 2019-06-06 23:39:51 UTC  
> Url: https://github.com/boostorg/type_traits/pull/122#issuecomment-499707094  

@jzmaddock this fix looks good and could unblock Windows ARM64 porting for any Boost consumer. Could this get merged?

---

## Comment 3

> Username: gongminmin  
> Created_at: 2019-06-07 02:54:42 UTC  
> Updated_at: 2019-06-07 02:54:59 UTC  
> Url: https://github.com/boostorg/type_traits/pull/122#issuecomment-499739875  

>   
>   
> > #endif // _MSC_VER  
>   
> Update the comment here according to the fix?  
>   
> Refers to: include/boost/type_traits/detail/is_function_cxx_11.hpp:562 in [ccbe9f7](https://github.com/boostorg/type_traits/commit/ccbe9f7abe799ef667760c6a59f95bfd5927e901). [](commit_id = [ccbe9f7](https://github.com/boostorg/type_traits/commit/ccbe9f7abe799ef667760c6a59f95bfd5927e901), deletion_comment = False)  
  
Just fixed this. Thanks.

---

## Comment 4

> Username: gongminmin  
> Created_at: 2019-06-10 05:42:11 UTC  
> Url: https://github.com/boostorg/type_traits/pull/122#issuecomment-500298261  

One of the appveyor job needs to be restarted. Any one can help? Thanks.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2019-06-10 08:31:01 UTC  
> Url: https://github.com/boostorg/type_traits/pull/122#issuecomment-500336225  

Restarted, will try to find the time to look at this shortly!

---

## Comment 6

> Username: gongminmin  
> Created_at: 2019-06-11 02:35:29 UTC  
> Url: https://github.com/boostorg/type_traits/pull/122#issuecomment-500663771  

Thanks.

---
