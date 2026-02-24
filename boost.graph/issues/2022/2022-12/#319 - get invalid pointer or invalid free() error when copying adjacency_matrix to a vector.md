# #319 - get invalid pointer or invalid free() error when copying adjacency_matrix to a vector [Open]

> Username: jakubo87  
> Created at: 2022-12-18 00:04:13 UTC  
> Updated at: 2023-01-11 22:39:01 UTC  
> Url: https://github.com/boostorg/graph/issues/319  

I have been trying to preload adjacency matrices with bundled properties to a std::vector of adjacency matrices in order to benchmark some uni project algorithm and not by chance measure the time to set up the graph.  
In that instance i tried different approaches to copy a prototype g0:  
- std::fill wouldn't work as AM is not a value type.  
- boost::copy_graph() wouldn't work due to AM not being a list type  
- using std::vector::emplace to construct the graph in place using a constructor declared in the documentation and the AM.hpp: one (of 2) with the edge iterators and num_nodes. * Luckily boost::edges(graph_t) provides such a thing. But it wouldn't work as the edge-iterator-pair provided does not include a .first member. There is one that needs an a property iterator ep_it. i haven't found any information on how do make one and if this is meant for bundled properties.  
- using std::vector::push_back(g0) which works until the final "}" and then crashes when freeing or with a "munmap_chunk(): invalid pointer" error. g0 being a predefined graph i want to fill the vector with. It seems plausible as AM seems to be copy constructible. But apparently not entirely. Or maybe the property maps aren't.  
However: after running through the algorithms there seems to be no change to g0. So it does not seem to be a "shallow copy" problem. Also valgrind does not report any memory leaks. But lots of errors.  
This also happens when copy constructing only 2 graphs.  
  
* on a sidenote: the documentation for iterator constructible suggests passing the number of edges too as it may speed up some processes. The AM does not seem to provide such a thing in the documentation in the section member functions (i.e. constructors).

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2023-01-03 00:20:42 UTC  
> Url: https://github.com/boostorg/graph/issues/319#issuecomment-1369288061  

What exactly do you mean that you can't use `std::fill` because `adjacency_matrix` is not a value type?

---

## Comment 2

> Username: jakubo87  
> Created at: 2023-01-11 22:39:01 UTC  
> Url: https://github.com/boostorg/graph/issues/319#issuecomment-1379574964  

> What exactly do you mean that you can't use `std::fill` because `adjacency_matrix` is not a value type?  
  
I might be wrong about this one. Though i don't seem to be able to build begin- and end-iterator from the property map. Or would it work with std::ranges::fill()?  
I have source code I can send you, if you want.
