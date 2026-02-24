# #126 - flat_set.hpp and set.hpp in pmr have the same header guard [Closed]

> Username: YuriAzathoth  
> Created at: 2019-07-26 12:26:33 UTC  
> Updated at: 2020-07-12 10:55:04 UTC  
> Closed at: 2020-07-12 10:53:54 UTC  
> Url: https://github.com/boostorg/container/issues/126  

The same header guard BOOST_CONTAINER_PMR_SET_HPP is used in both pmr/set.hpp and pmr/flat_set.hpp.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-07-12 10:55:04 UTC  
> Url: https://github.com/boostorg/container/issues/126#issuecomment-657205939  

This fix was missed in recent releases, but fixed at last. Many thanks for the report!
