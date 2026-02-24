# #274 - Make time until next timer fire available [Closed]

> Username: xim  
> Created at: 2019-08-26 10:47:50 UTC  
> Updated at: 2020-12-30 01:08:25 UTC  
> Closed at: 2020-12-30 01:08:24 UTC  
> Url: https://github.com/boostorg/asio/issues/274  

Hi,  
  
I am writing tests for an application that uses boost::asio. To be able to stub out time in these tests, I have done roughly as follows:  
  
* Written stub implementations of time related functions like `clock_gettime()` that use a static counter as a backend for time.  
* Made a test fixture called `BoostTimeStub` that grabs a hold of a reactor using `boost::asio::use_service<boost::asio::detail::reactor>()`  
   * This stub uses `reactor.get_timeout(-1)` to determine when the next timer fire is expected  
   * To have the epoll reactor behave correctly, I have also stubbed `timerfd_create()`  
* In tests for code that relies on time moving forward, I can call `BoostTimeStub::wait` to have it simulate progressing time.  
  
I can supply a more complete example if desired, but essentially the only obstacle in creating this was the fact that `boost::asio::detail::reactor::get_timeout()` is private in all implementations. I have patched the local version of boost to add a strategically placed `public:` line above the declaration of `epoll_reactor::get_timeout(int)` but would like to avoid making the test code dependent on a fork of boost.  
  
Is there any way that the time until next timer fire could be returned from an `io_context` besides this, or any way an API for this could be added?

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:08:23 UTC  
> Url: https://github.com/boostorg/asio/issues/274#issuecomment-752292762  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#713](https://github.com/chriskohlhoff/asio/issues/713).
