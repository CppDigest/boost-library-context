# #46 - Test streams should use fail_count [Closed]

> Username: vinniefalco  
> Created at: 2023-01-19 15:59:59 UTC  
> Updated at: 2023-09-02 13:03:36 UTC  
> Closed at: 2023-09-02 13:03:36 UTC  
> Url: https://github.com/boostorg/redis/issues/46  

One of the main test modes of `test::stream` is that it can fail on the Nth operation, for increasing values of N. This ensures that every possible branch in the code being tested is exercised:  
  
https://github.com/boostorg/beast/blob/97ece405b8127e1d4767a8f63b82478d5637b9ec/include/boost/beast/_experimental/test/fail_count.hpp#L31  
  
The tests should be constructing the `test::stream` with a `fail_count`, and then running the same test over and over again in a loop with incrementing `fail_count` until the test passes.

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-09-02 13:03:36 UTC  
> Url: https://github.com/boostorg/redis/issues/46#issuecomment-1703827218  

PR https://github.com/boostorg/redis/pull/151 has removed the `resp3::read` and `async_read` functions and therefore we don't need the beast test stream anymore. The way the parser works now enables testing without any concept of stream.
