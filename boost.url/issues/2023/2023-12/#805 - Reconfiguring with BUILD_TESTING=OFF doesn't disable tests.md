# #805 - Reconfiguring with BUILD_TESTING=OFF doesn't disable tests [Closed]

> Username: pdimov  
> Created at: 2023-12-17 01:20:44 UTC  
> Updated at: 2023-12-22 15:05:56 UTC  
> Closed at: 2023-12-22 15:05:56 UTC  
> Url: https://github.com/boostorg/url/issues/805  

When URL is configured with `cmake -DBUILD_TESTING=ON ..`, and then reconfigured with `cmake -DBUILD_TESTING=OFF .`, tests remain enabled. That's because `BUILD_TESTING` is only used once as the default value of `BOOST_URL_BUILD_TESTS` and is ignored afterwards.  
  
That's not how all other Boost libraries work; they check `BUILD_TESTING` directly and do not `add_subdirectory(test)` if it's off.
