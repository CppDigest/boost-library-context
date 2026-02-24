# #97 - Reconfiguring with BUILD_TESTING=OFF doesn't disable tests [Closed]

> Username: pdimov  
> Created at: 2023-12-17 01:39:17 UTC  
> Updated at: 2023-12-17 07:12:08 UTC  
> Closed at: 2023-12-17 07:12:07 UTC  
> Url: https://github.com/boostorg/lockfree/issues/97  

When Lockfree is configured with `cmake -DBUILD_TESTING=ON ..`, and then reconfigured with `cmake -DBUILD_TESTING=OFF .`, tests remain enabled. That's because `BUILD_TESTING` is only used once as the default value of `BOOST_LOCKFREE_BUILD_TESTS` and is ignored afterwards.  
  
That's not how all other Boost libraries work; they check `BUILD_TESTING` directly and do not `add_subdirectory(test)` if it's off.

---

## Comment 1

> Username: timblechmann  
> Created at: 2023-12-17 07:12:07 UTC  
> Url: https://github.com/boostorg/lockfree/issues/97#issuecomment-1859058206  

#96 should fix it
