# #28 Feature/setops [Merged]

> Username: mkaravel  
> Created at: 2014-05-14 12:41:52 UTC  
> Updated at: 2014-05-14 12:45:41 UTC  
> Merged at: 2014-05-14 12:45:20 UTC  
> Closed at: 2014-05-14 12:45:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/28  

fix unused variable warnings produced with clang++ and -Wextra

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-05-14 12:45:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/28#issuecomment-43075296  

Merged too  
b.  
  
Menelaos Karavelas wrote On 14-5-2014 14:41:  
  
> fix unused variable warnings produced with clang++ and -Wextra  
>   
> ---  
>   
> ```  
>     You can merge this Pull Request by running  
> ```  
>   
>    git pull https://github.com/mkaravel/geometry feature/setops  
>   
> Or view, comment on, or merge it at:  
>   
> https://github.com/boostorg/geometry/pull/28  
>   
> ```  
>     Commit Summary  
> ```  
> - [set ops L/L] fix unused variable warnings produced with clang++  
>   with -Wextra  
> - [test][set ops L/L] fix unused variable warnings produced with  
>   clang++ and -Wextra  
> - [test] fix unused variable warnings produced with clang++ and -Wextra  
>     
>   ```  
>   File Changes  
>   ```  
> - _M_  
>   include/boost/geometry/algorithms/detail/overlay/follow_linear_linear.hpp  
>   https://github.com/boostorg/geometry/pull/28/files#diff-0 (2)  
> - _M_  
>   include/boost/geometry/algorithms/detail/turns/filter_continue_turns.hpp  
>   https://github.com/boostorg/geometry/pull/28/files#diff-1 (2)  
> - _M_  
>   include/boost/geometry/algorithms/detail/turns/remove_duplicate_turns.hpp  
>   https://github.com/boostorg/geometry/pull/28/files#diff-2 (2)  
> - _M_ test/algorithms/test_set_ops_linear_linear.hpp  
>   https://github.com/boostorg/geometry/pull/28/files#diff-3 (2)  
> - _M_ test/to_svg.hpp  
>   https://github.com/boostorg/geometry/pull/28/files#diff-4 (4)  
>     
>   ```  
>   Patch Links:  
>   ```  
> - https://github.com/boostorg/geometry/pull/28.patch  
> - https://github.com/boostorg/geometry/pull/28.diff  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/geometry/pull/28.  
  
##   
  
Barend Gehrels  
http://about.me/barendgehrels

---
