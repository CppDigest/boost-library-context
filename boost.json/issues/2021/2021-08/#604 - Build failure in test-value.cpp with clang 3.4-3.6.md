# #604 - Build failure in test/value.cpp with clang 3.4-3.6 [Closed]

> Username: grisumbras  
> Created at: 2021-08-07 14:13:04 UTC  
> Updated at: 2022-11-16 14:42:13 UTC  
> Closed at: 2022-11-16 14:42:12 UTC  
> Url: https://github.com/boostorg/json/issues/604  

https://www.boost.org/development/tests/master/developer/output/teeks99-dkr-mc3-4-11-boost-bin-v2-libs-json-test-value-test-clang-linux-3-4~c++11-debug-debug-symbols-off-threading-multi-visibility-hidden.html  
  
This seems to be a problem with older clangs' parsing of `{{}}`. Given that this in a test, we probably can just remove initializer list with something more explicit (`value({{}})` should be equivalent to `value(array(object()))`).

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-11-16 14:42:12 UTC  
> Url: https://github.com/boostorg/json/issues/604#issuecomment-1317129738  

Fixed in 1d7cbc2e55a344983e7192cf9e2c8886ea9ee3d0
