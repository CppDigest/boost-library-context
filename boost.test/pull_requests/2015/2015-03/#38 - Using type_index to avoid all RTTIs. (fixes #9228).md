# #38 Using type_index to avoid all RTTIs. (fixes #9228) [Closed]

> Username: gongminmin  
> Created at: 2015-03-04 05:53:40 UTC  
> Updated at: 2015-03-12 14:00:46 UTC  
> Closed at: 2015-03-12 14:00:46 UTC  
> Url: https://github.com/boostorg/test/pull/38  

A better solution than #ifndef BOOST_NO_RTTI.

---

## Comment 1

> Username: rogeeff  
> Created_at: 2015-03-04 20:10:37 UTC  
> Url: https://github.com/boostorg/test/pull/38#issuecomment-77236199  

I'm not sure we want to introduce new dependency for Boost.Test.  
  
On Wed, Mar 4, 2015 at 12:53 AM, Minmin Gong notifications@github.com  
wrote:  
  
> ---  
>   
> You can view, comment on, or merge this pull request online at:  
>   
>   https://github.com/boostorg/test/pull/38  
> Commit Summary  
> - Using type_index to avoid all RTTIs. (fixes #9228)  
>   
> File Changes  
> - _M_ include/boost/test/execution_monitor.hpp  
>   https://github.com/boostorg/test/pull/38/files#diff-0 (13)  
> - _M_ include/boost/test/tree/test_case_template.hpp  
>   https://github.com/boostorg/test/pull/38/files#diff-1 (13)  
> - _M_ include/boost/test/utils/runtime/interpret_argument_value.hpp  
>   https://github.com/boostorg/test/pull/38/files#diff-2 (3)  
> - _M_ test/test_datasets_src/test_datasets.hpp  
>   https://github.com/boostorg/test/pull/38/files#diff-3 (3)  
>   
> Patch Links:  
> - https://github.com/boostorg/test/pull/38.patch  
> - https://github.com/boostorg/test/pull/38.diff  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/38.  
  
##   
  
Gennadiy Rozental

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2015-03-11 22:35:19 UTC  
> Url: https://github.com/boostorg/test/pull/38#issuecomment-78387703  

The issue 9228 is already fixed in develop. Any other motivation behind the changes you propose?

---

## Comment 3

> Username: gongminmin  
> Created_at: 2015-03-12 01:33:21 UTC  
> Url: https://github.com/boostorg/test/pull/38#issuecomment-78408395  

That fix uses #ifdef to separate RTTI on and off. If we switch to type_index, we can have a unified code.

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2015-03-12 14:00:46 UTC  
> Url: https://github.com/boostorg/test/pull/38#issuecomment-78484355  

Ok, but this is rather cosmetic than a fix to an identified problem. The issue 9228 has already been addressed.

---
