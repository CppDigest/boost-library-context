# #7 Add std::initializer_list support for boost::container::vector [Merged]

> Username: robert-matusewicz  
> Created at: 2014-08-16 10:02:55 UTC  
> Updated at: 2014-08-21 06:01:53 UTC  
> Merged at: 2014-08-17 08:55:05 UTC  
> Closed at: 2014-08-17 08:55:05 UTC  
> Url: https://github.com/boostorg/container/pull/7  

This pull request adds support for std::initializer_list for boost::container::vector.  
  
Additionally, I converted method that was used to test stable_vector to templated version to remove code duplication in tests.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-08-17 08:55:56 UTC  
> Url: https://github.com/boostorg/container/pull/7#issuecomment-52416937  

Thanks for the pull request. Tested in several C++03 and C++11 compilers with no errors.

---
