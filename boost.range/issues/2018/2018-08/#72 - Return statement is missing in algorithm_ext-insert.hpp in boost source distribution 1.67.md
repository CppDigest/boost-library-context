# #72 - Return statement is missing in algorithm_ext/insert.hpp in boost source distribution 1.67 [Open]

> Username: timpatt  
> Created at: 2018-08-03 05:50:57 UTC  
> Updated at: 2019-05-23 21:09:06 UTC  
> Url: https://github.com/boostorg/range/issues/72  

The version of the `boost/range/algorithm_ext/insert.hpp` file that is included with the source distribution downloaded from the main [boost download page](https://www.boost.org/users/history/version_1_67_0.html) still has the return bug that was apparently fixed many years ago in this commit: 474c62ab16e72153535883fd83c78dab62fc7967.

---

## Comment 1

> Username: mjendruk  
> Created at: 2019-05-23 21:09:06 UTC  
> Url: https://github.com/boostorg/range/issues/72#issuecomment-495385784  

I checked the tag 1.68.0 as well as the downloadable version from https://www.boost.org/users/history/version_1_68_0.html. The fix was merged in this release.   
  
This issue can be closed.
