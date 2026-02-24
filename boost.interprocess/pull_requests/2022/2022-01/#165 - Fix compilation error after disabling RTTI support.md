# #165 Fix compilation error after disabling RTTI support [Closed]

> Username: cngzhnp  
> Created at: 2022-01-01 12:59:17 UTC  
> Updated at: 2024-07-06 21:09:48 UTC  
> Closed at: 2024-07-06 21:09:48 UTC  
> Url: https://github.com/boostorg/interprocess/pull/165  

I tried to fix that issue by adding `BOOST_NO_RTTI ` preprocessor definition and type_index solution of Boost itself which I created PR #164.  
  
In my opinion, `dynamic_cast_tag` struct must be supported if only RTTI is not disabled via this flag. Because of that reason, I just wrapped up with `BOOST_NO_RTTI ` preprocessor where it is used. If you disagree with that, you can offer another solution for that.  
  
I do not edit any documentation or test code without confirmation, please review this and let me know which parts need to be changed on documentation and test codes.  
  
Thanks in advance.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2024-07-06 21:09:48 UTC  
> Url: https://github.com/boostorg/interprocess/pull/165#issuecomment-2211969111  

Reviewing old bugs.  
  
Unfortunately Boost.Interprocess is not designed to build without RTTI or exceptions and there are no plans to support those systems as environments with shared memory or memory mapped files are supposedly complex enough to afford them.

---
