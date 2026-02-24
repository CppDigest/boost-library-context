# #445 cmake: Add `BOOST_TEST_HEADERS_ONLY` configuration variable [Merged]

> Username: hebasto  
> Created at: 2025-04-16 18:42:53 UTC  
> Updated at: 2025-04-17 13:12:48 UTC  
> Merged at: 2025-04-17 12:46:46 UTC  
> Closed at: 2025-04-17 12:46:46 UTC  
> Url: https://github.com/boostorg/test/pull/445  

This PR allows the build to be configured to install only the Boost.Test headers required for using the [headers-only variant](https://www.boost.org/doc/libs/1_88_0/libs/test/doc/html/boost_test/usage_variants.html#boost_test.usage_variants.single_header) of the Unit Test Framework.  
  
The [`README.md`](https://github.com/boostorg/cmake/blob/develop/README.md) file at https://github.com/boostorg/cmake should also be updated to mention this new Boost.Test-specific configuration variable.

---

## Comment 1

> Username: mborland  
> Created_at: 2025-04-16 19:56:19 UTC  
> Url: https://github.com/boostorg/test/pull/445#issuecomment-2810617390  

Hi, @hebasto. This seems like a reasonable change to me. I have https://github.com/boostorg/test/pull/446 cycling to fix up the CI system since 20.04 is being removed from Github Actions. Once the CI system is back to green, I'll pull it into this branch for you.

---

## Comment 2

> Username: hebasto  
> Created_at: 2025-04-17 13:12:46 UTC  
> Url: https://github.com/boostorg/test/pull/445#issuecomment-2812853762  

> The [`README.md`](https://github.com/boostorg/cmake/blob/develop/README.md) file at https://github.com/boostorg/cmake should also be updated to mention this new Boost.Test-specific configuration variable.  
  
Done in https://github.com/boostorg/cmake/pull/82.

---
