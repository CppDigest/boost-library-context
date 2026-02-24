# #254 - C++20 std::erase_if [Closed]

> Username: mglisse  
> Created at: 2023-10-07 09:22:02 UTC  
> Updated at: 2025-11-01 21:11:51 UTC  
> Closed at: 2025-11-01 21:11:51 UTC  
> Url: https://github.com/boostorg/container/issues/254  

C++20 added a uniform interface `std::erase_if` to remove some elements from a container, with overloads for all the standard containers. I believe it would be useful to implement this for the containers in Boost. My only doubt is whether the overloads should be in namespace std or in the namespace of the container (probably the second, at least that's what the original WG21 paper suggested and what Boost.Unordered did).

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-11-01 21:11:51 UTC  
> Url: https://github.com/boostorg/container/issues/254#issuecomment-3476849104  

After commit https://github.com/boostorg/container/commit/22bc5024d580f1c79300a5892b01299411567168, all Boost.Container containers have erase_if overloads in the `boost::container` namespace. Many thanks for the issue!
