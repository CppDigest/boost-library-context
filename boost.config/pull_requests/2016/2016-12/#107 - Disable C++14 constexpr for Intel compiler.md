# #107 Disable C++14 constexpr for Intel compiler [Merged]

> Username: Lastique  
> Created at: 2016-12-11 12:46:17 UTC  
> Updated at: 2016-12-13 18:10:42 UTC  
> Merged at: 2016-12-13 18:10:41 UTC  
> Closed at: 2016-12-13 18:10:42 UTC  
> Url: https://github.com/boostorg/config/pull/107  

Intel compiler up to version 17.0 (on Linux) makes constexpr member functions implicitly const-qualified.  
  
This PR is a continuation of https://github.com/boostorg/config/pull/104 and http://lists.boost.org/Archives/boost/2016/12/231859.php.

---
