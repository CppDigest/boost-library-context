# #31 Fix three test failures on older and c++03 compiler. [Merged]

> Username: Flast  
> Created at: 2014-11-02 06:20:29 UTC  
> Updated at: 2014-11-02 12:49:23 UTC  
> Merged at: 2014-11-02 12:49:23 UTC  
> Closed at: 2014-11-02 12:49:23 UTC  
> Url: https://github.com/boostorg/fusion/pull/31  

This PR contains various commits since merge back master into develop to be consistent with master branch.  
  
The key commits of this PR are following.  
- f73b536 Fix test error with C++03 mode, due to C++11 using-declaration.  
- 1b4acaa Fix unexpected lookup failure with GCC < 4.5.  
- 2661fc7 Change the result_of behaviour with BOOST_RESULT_OF_USE_DECLTYPE

---

## Comment 1

> Username: djowel  
> Created_at: 2014-11-02 12:49:19 UTC  
> Url: https://github.com/boostorg/fusion/pull/31#issuecomment-61405382  

Wonderful, as always, Kohei!

---
