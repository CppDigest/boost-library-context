# #272 - Uninitialized memory in framework_init_observer_t [Closed]

> Username: Barath-Kannan  
> Created at: 2020-05-18 06:59:35 UTC  
> Updated at: 2022-03-01 22:36:51 UTC  
> Closed at: 2022-03-01 22:36:50 UTC  
> Url: https://github.com/boostorg/test/issues/272  

test_framework_init_observer.hpp:51  
  
should be;  
`bool m_has_failure{ false };`  
  
This was causing errors in empty tests in gcc 9.1 and clang 8.0.1 at impl/framework.ipp:1672  
`if( local_init_observer.has_failed() )`

---

## Comment 1

> Username: gouriano  
> Created at: 2021-12-01 13:50:52 UTC  
> Url: https://github.com/boostorg/test/issues/272#issuecomment-983660096  

It seems there is a much bigger problem with that.  
While the framework is supposed to call "framework_init_observer_t::test_start", which initializes m_has_failure,  
it calls base class function "test_observer::test_start" instead (I use GCC 7.3.0).  
I do not know where the problem is.  
Also, on Windows/VisualStudio2019 it works correctly - framework_init_observer_t::test_start is called.
