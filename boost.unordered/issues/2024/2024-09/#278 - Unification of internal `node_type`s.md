# #278 - Unification of internal `node_type`s [Closed]

> Username: joaquintides  
> Created at: 2024-09-02 11:29:53 UTC  
> Updated at: 2024-09-04 17:19:05 UTC  
> Closed at: 2024-09-04 17:19:05 UTC  
> Url: https://github.com/boostorg/unordered/issues/278  

The following containers support `node_type` insertion/extraction:  
  
1. `boost::unordered_map` `boost::unordered_set` `boost::unordered_multimap` `boost::unordered_multiset`   
2. `boost::unordered_node_map` `boost::unordered_node_set` `boost::concurrent_node_map` `boost::concurrent_node_set`  
  
Internally, however, containers in (1) use [`boost::unordered::node_handle_map`](https://github.com/boostorg/unordered/blob/21937249c47da1fbdb445dc40775a88832fd772d/include/boost/unordered/unordered_map.hpp#L2174-L2289) and [`boost::unordered::node_handle_set`](https://github.com/boostorg/unordered/blob/21937249c47da1fbdb445dc40775a88832fd772d/include/boost/unordered/unordered_set.hpp#L1801-L1910), while those in (2) use [`boost::unordered::detail::foa::node_map_handle`](https://github.com/boostorg/unordered/blob/21937249c47da1fbdb445dc40775a88832fd772d/include/boost/unordered/detail/foa/node_map_handle.hpp) and [`boost::unordered::detail::foa::node_set_handle`](https://github.com/boostorg/unordered/blob/21937249c47da1fbdb445dc40775a88832fd772d/include/boost/unordered/detail/foa/node_set_handle.hpp). This provides an opportunity for refactoring and further introperability between containers in Boost.Unordered.  
  
* Refactor containers in (1) to use the `node_type`s defined for (2)  
* Move [`boost::unordered::detail::foa::node_map_handle`](https://github.com/boostorg/unordered/blob/21937249c47da1fbdb445dc40775a88832fd772d/include/boost/unordered/detail/foa/node_map_handle.hpp) and [`boost::unordered::detail::foa::node_set_handle`](https://github.com/boostorg/unordered/blob/21937249c47da1fbdb445dc40775a88832fd772d/include/boost/unordered/detail/foa/node_set_handle.hpp) to `boost::unordered::detail`, and their corresponding header files to `boost/unordered/detail`.  
* Document node compatibility between containers in Boost.Unordered. For reference, [this](https://eel.is/c++draft/container.node.overview#tab:container.node.compat) is how the standard does it, but we may come up with something more ergonomic,

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-09-04 17:19:05 UTC  
> Url: https://github.com/boostorg/unordered/issues/278#issuecomment-2329601717  

Upon analysis, nodes in (1) and (2) are indeed structurally incompatible, so unification, though possible, seems too much for the potential benefits. Closing.
