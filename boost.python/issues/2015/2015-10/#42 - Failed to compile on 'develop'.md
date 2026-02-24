# #42 - Failed to compile on 'develop' [Closed]

> Username: lynnboy  
> Created at: 2015-10-09 01:37:58 UTC  
> Updated at: 2016-09-01 04:25:23 UTC  
> Closed at: 2016-09-01 04:25:23 UTC  
> Url: https://github.com/boostorg/python/issues/42  

Caused by commit `b2b9ab1`, "Remove unused deprecated includes" of _Boost.Iterator_  
  
Please change `boost::detail::distance` to `std::distance` in `include/boost/python/slice.hpp` (2 occurrances).

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2016-03-06 18:19:45 UTC  
> Url: https://github.com/boostorg/python/issues/42#issuecomment-192954306  

Can you elaborate a bit what you mean by 'failed to compile' ? I'm on Fedora 23 (x86_64), and everything builds fine, both on the develop as well as the master branch.  
  
What platform are you on (OS, compiler, etc.), what steps did you follow, and what is the exact failure you are observing ?  
  
Thanks,

---

## Comment 2

> Username: lynnboy  
> Created at: 2016-03-07 16:12:01 UTC  
> Url: https://github.com/boostorg/python/issues/42#issuecomment-193320390  

Sorry, this has been handled by pull request #43. You'd better apply that as a direct solution.  
This has been caused by a change of boost::detail repository on develop branch, which removed the `boost::detail::distance` as a typedef of `std::distance`.  
I'm using Cygwin for building Boost.  
  
More details:  
`boost/detail/iterator.hpp` was included indirectly through `boost/iterator/iterator_traits.hpp` and `python/slices.hpp` was using `boost::detail::distance()`. But at Sep 9, 2015 the inclusion within Boost.Iterator library has been cleaned away because of deprecation from Boost.Core for entire `boost/iterator.hpp` and `boost/detail/iterator.hpp` headers. This change was merged into Boost.Iterator's master branch, but has been reverted again at Oct 15, 2015. Thus Boost.Python can successfully build with Boost.Iterator with newest master branch, but compilation failure occurs on Boost.Iterator's develop branch.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2016-09-01 04:25:23 UTC  
> Url: https://github.com/boostorg/python/issues/42#issuecomment-243972688  

https://github.com/boostorg/python/pull/43 has just been merged. Closing...
