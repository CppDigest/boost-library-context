# #5 Fix the use of constexpr in msvc-12.0 [Merged]

> Username: awulkiew  
> Created at: 2014-05-18 00:21:49 UTC  
> Updated at: 2014-08-12 12:39:03 UTC  
> Merged at: 2014-06-09 19:36:44 UTC  
> Closed at: 2014-06-09 19:36:44 UTC  
> Url: https://github.com/boostorg/ublas/pull/5  

Currently Ublas doesn't compile on msvc-12.0 because of invalid use of constexpr keyword.  
This fix includes <boost/config.hpp> and replaces constexpr with BOOST_CONSTEXPR which should expand to nothing on compilers not supporting this keyword.

---

## Comment 1

> Username: yimyom  
> Created_at: 2014-05-18 09:57:20 UTC  
> Url: https://github.com/boostorg/ublas/pull/5#issuecomment-43435795  

Thanks Adam for your patch. Let me review them quickly and I'll push them  
into our main dev branch  
  
On Sun, May 18, 2014 at 1:21 AM, Adam Wulkiewicz  
notifications@github.comwrote:  
  
> Currently Ublas doesn't compile on msvc-12.0 because of invalid use of  
> constexpr keyword.  
> This fix includes and replaces constexpr with BOOST_CONSTEXPR which should  
>   
> ## expand to nothing on compilers not supporting this keyword.  
>   
> You can merge this Pull Request by running  
>   
>   git pull https://github.com/awulkiew/ublas fix/constexpr  
>   
> Or view, comment on, or merge it at:  
>   
>   https://github.com/boostorg/ublas/pull/5  
> Commit Summary  
> - Fix unused variable warnings  
> - Fix msvc-12.0 constexpr errors  
>   
> File Changes  
> - _M_ include/boost/numeric/ublas/matrix.hpphttps://github.com/boostorg/ublas/pull/5/files#diff-0(17)  
> - _M_ include/boost/numeric/ublas/storage.hpphttps://github.com/boostorg/ublas/pull/5/files#diff-1(4)  
> - _M_ include/boost/numeric/ublas/vector.hpphttps://github.com/boostorg/ublas/pull/5/files#diff-2(9)  
> - _M_ include/boost/numeric/ublas/vector_proxy.hpphttps://github.com/boostorg/ublas/pull/5/files#diff-3(4)  
>   
> Patch Links:  
> - https://github.com/boostorg/ublas/pull/5.patch  
> - https://github.com/boostorg/ublas/pull/5.diff  
>   
> —  
> Reply to this email directly or view it on GitHubhttps://github.com/boostorg/ublas/pull/5  
> .

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-05-18 11:56:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/5#issuecomment-43437989  

Ok, thanks!  
I just saw that ignore_unused_variable_warning() is also defined internally in uBlas, in ublas/detail/definitions.hpp so this can be used to fix the issue mentioned in the comment of PR #4. Let me know if you want me to do it.

---
