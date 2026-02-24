# #453 Add Louvain community detection algorithm [Open]

> Username: Becheler  
> Created at: 2026-02-02 13:01:23 UTC  
> Updated at: 2026-02-23 20:46:59 UTC  
> Url: https://github.com/boostorg/graph/pull/453  

Implement #447   
  
- Multi-level modularity optimization following Blondel et al (2008)  
- Supports custom quality functions (other than modularity) with policy-based design (extensions to come to propose alternative quality functions to match gen-louvain)  
- Incremental quality tracking  
- Lazy rollback  
- Vertex shuffling  
- Competitive with established implementations (I will work on making the benchmarks reproducible)  
  
But:  
  
- documentation is not ready  
- example is not ready  
- just realized I forgot to test  with different graph types + boost concepts  
  
<img width="4303" height="4978" alt="benchmark_comparison" src="https://github.com/user-attachments/assets/a4340a73-c10f-4e47-b4c5-79689c4bdad1" />

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2026-02-04 22:53:34 UTC  
> Url: https://github.com/boostorg/graph/pull/453#issuecomment-3850143731  

Not sure why this error is occurring only for OSX 11.7 C++14. I assume this is not specific to your code.  
```  
In file included from ../../../boost/container/detail/operator_new_helpers.hpp:26:  
../../../boost/container/detail/aligned_allocation.hpp:87:26: error: no member named 'aligned_alloc' in the global namespace; did you mean 'aligned_allocate'?  
   return rounded_size ? ::aligned_alloc(al, rounded_size) : 0;  
                         ^~~~~~~~~~~~~~~  
                         aligned_allocate  
../../../boost/container/detail/aligned_allocation.hpp:77:14: note: 'aligned_allocate' declared here  
inline void* aligned_allocate(std::size_t al, std::size_t sz)  
             ^  
1 error generated.  
```

---

## Review 2 [Commented]

> Username: jeremy-murphy  
> Created_at: 2026-02-04 23:06:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3753688354  

Just a few comments, more later.

📁 include/boost/graph/louvain_clustering.hpp

```diff
  10 |+ //
  11 |+ // Revision History:
  12 |+ //
```

> Username: jeremy-murphy  
> Created_at: 2026-02-04 22:56:15 UTC  
> Updated_at: 2026-02-04 23:06:02 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2766321025  

I know embedded revision histories were a thing in the past, but I would prefer to keep all this metadata in git.

> Username: Becheler  
> Created_at: 2026-02-04 23:33:52 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2766412033  

I did not like it either, I'm glad you don't ahah :)

> Username: Becheler  
> Created_at: 2026-02-09 15:22:19 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2783240001  

Done 👍🏽

---

📁 include/boost/graph/louvain_clustering.hpp

```diff
  42 |+     PartitionMap partition;
  43 |+     std::map<VertexDescriptor, WeightType> internal_weights;
  44 |+     std::map<VertexDescriptor, std::set<VertexDescriptor>> vertex_mapping;
```

> Username: jeremy-murphy  
> Created_at: 2026-02-04 22:59:53 UTC  
> Updated_at: 2026-02-04 23:06:02 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2766329312  

We generally try to avoid hard-coding the choice of data structure, especially `std::map` and `std::set`, so instead of templating `VertexDescriptor` and `WeightType` we should template the whole map type, so users can use `boost::unordered_map` or some other kind of property map of their own choice.

> Username: Becheler  
> Created_at: 2026-02-04 23:37:21 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2766419171  

Mhhh I see. I felt it was not in the BGL spirit to do so, and Joaquin also mentioned it, but I was not sure how to solve it without making the API heavy. Can we default to a concrete type to simplify user's experience ? Also, is it ok to use `boost::unordered_map` if it adds the constraint of `key_type` being hashable ? That was my idea behind using `std::map`

> Username: jeremy-murphy  
> Created_at: 2026-02-08 01:09:44 UTC  
> Updated_at: 2026-02-08 01:10:26 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2778319869  

Oh yes, we can still (and should) make the user experience nice with defaults. That's the great thing, we get both benefits, the cost is more work on the part of the library authors. :)  
Again, I think `astar` is an example, but instead of using `param =  arg` in the function definition, make an overload, like so:   
  
```  
auto user_friendly_foo(graph const &g) {  
ConcreteA a;  
ConcreteB b;  
return generic_foo(g, a, b);  
}  
```  
  
Sorry, typing on my phone, so please ignore random syntax errors.

> Username: jeremy-murphy  
> Created_at: 2026-02-08 01:32:38 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2778350385  

Umm, yeah, we probably shouldn't add new constraints into the default interface. Users will too easily assume that the constraint is mandatory.   
So maybe use boost::flat_map as the default and users can always use a hash map if they want to.

---

📁 include/boost/graph/louvain_clustering.hpp

```diff
  68 |+     std::set<community_type> unique_communities;
  69 |+     std::map<community_type, vertex_descriptor> comm_to_vertex;
  70 |+     std::map<vertex_descriptor, std::set<vertex_descriptor>> vertex_to_originals;
```

> Username: jeremy-murphy  
> Created_at: 2026-02-04 23:05:44 UTC  
> Updated_at: 2026-02-04 23:06:02 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2766344280  

These should almost certainly be input parameters taken by non-const reference so that the user a) decides their type and b) automatically gets their value at the end.

> Username: Becheler  
> Created_at: 2026-02-04 23:39:14 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2766423329  

So they gain access to the whole hierarchy. Ufff it's a lot of guts leaking out haha   
Will do, and tell you in case of problemsm thanks again for your time !

> Username: jeremy-murphy  
> Created_at: 2026-02-05 02:37:27 UTC  
> Updated_at: 2026-02-05 02:37:28 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2766790787  

I could be wrong. But have a look at the `astar` API for some examples of prior art.

> Username: jeremy-murphy  
> Created_at: 2026-02-08 01:36:54 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2778359242  

And on second thought, this is not a priority, we can always do it later. Getting it correct and fast are higher priorities.


---

## Review 3 [Commented]

> Username: joaquintides  
> Created_at: 2026-02-06 18:27:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3764559570  

📁 include/boost/graph/louvain_clustering.hpp

```diff
   2 |+ // Copyright 2026 Becheler Code Labs for C++ Alliance
   3 |+ // Authors: Arnaud Becheler
   4 |+ //
```

> Username: joaquintides  
> Created_at: 2026-02-06 18:27:26 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2775466746  

You retain the copyright, so no need to mention the C++ Alliance. Also, is "Becheler Code Labs" a real legal entity? If this is not the case, then assigning (C) to your physical person would be best.

> Username: Becheler  
> Created_at: 2026-02-09 15:05:09 UTC  
> Updated_at: 2026-02-09 15:05:10 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2783149667  

Done 👍🏽


---

## Review 4 [Commented]

> Username: joaquintides  
> Created_at: 2026-02-06 18:48:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3764637798  

📁 include/boost/graph/louvain_clustering.hpp

```diff
 161 |+     {
 162 |+         assert(!empty());
 163 |+ 
```

> Username: joaquintides  
> Created_at: 2026-02-06 18:48:24 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2775542091  

Not sure what the BGL convention is, Boost libs generally use `BOOST_ASSERT` instead.

> Username: jeremy-murphy  
> Created_at: 2026-02-08 08:04:51 UTC  
> Updated_at: 2026-02-08 10:12:29 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2778866701  

I don't really mind, but yeah, better to follow whatever is common practice.

> Username: Becheler  
> Created_at: 2026-02-09 15:05:32 UTC  
> Updated_at: 2026-02-09 15:05:33 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2783151645  

Done 👍🏽


---

## Review 5 [Commented]

> Username: joaquintides  
> Created_at: 2026-02-06 18:50:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3764645995  

📁 include/boost/graph/louvain_clustering.hpp

```diff
 169 |+             
 170 |+             // From coarse to fine
 171 |+             for (int level = size() - 1; level >= 0; --level) {
```

> Username: joaquintides  
> Created_at: 2026-02-06 18:50:51 UTC  
> Updated_at: 2026-02-06 18:53:11 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2775550722  

Type mismatch, `size()` is a `std::size_t`. More idiomatic, but maybe not to your taste:  
  
```cpp  
for(auto level = size(); level--; ) {  
```

> Username: Becheler  
> Created_at: 2026-02-09 15:22:35 UTC  
> Updated_at: 2026-02-09 15:22:36 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2783241475  

Done 👍🏽


---

## Review 6 [Commented]

> Username: joaquintides  
> Created_at: 2026-02-06 19:05:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3764692668  

📁 include/boost/graph/louvain_clustering.hpp

```diff
 214 |+ template <typename Graph, typename CommunityMap, typename WeightMap, typename QualityFunction = newman_and_girvan>
 215 |+ typename property_traits<WeightMap>::value_type
 216 |+ louvain_local_optimization(
```

> Username: joaquintides  
> Created_at: 2026-02-06 19:05:01 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2775598477  

Is this function supposed to be public?

> Username: Becheler  
> Created_at: 2026-02-09 14:57:40 UTC  
> Updated_at: 2026-02-09 14:57:41 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2783110010  

I was not too sure, as it can theoretically be used as a one pass optimization step. But you're right it's obviously not the main usage, so I move it to details :)

> Username: Becheler  
> Created_at: 2026-02-09 15:22:46 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2783242396  

Done 👍🏽


---

## Review 7 [Commented]

> Username: joaquintides  
> Created_at: 2026-02-07 09:41:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3766785531  

📁 include/boost/graph/louvain_quality_functions.hpp

```diff
  70 |+ // k_c = sum of degrees in community c
  71 |+ // m = total edge weight / 2
  72 |+ struct newman_and_girvan
```

> Username: joaquintides  
> Created_at: 2026-02-07 09:41:45 UTC  
> Updated_at: 2026-02-07 09:41:58 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2777330574  

If users can provide their own quality function other than `newman_and_girvan`, you probably need to define a concept `LouvainQuality` of sorts. Take a look at how's done at  
  
https://github.com/boostorg/graph/blob/develop/include/boost/graph/astar_search.hpp#L56

> Username: Becheler  
> Created_at: 2026-02-16 13:21:50 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2812416272  

Done 👍🏽   
I defined:  
- `GraphPartitionQualityFunctionConcept`  
- `GraphPartitionQualityFunctionIncrementalConcept`


---

## Review 8 [Commented]

> Username: joaquintides  
> Created_at: 2026-02-07 10:30:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3766866601  

📁 include/boost/graph/louvain_clustering.hpp

```diff
 244 |+     
 245 |+     // Randomize vertex order once
 246 |+     std::mt19937 gen(seed);
```

> Username: joaquintides  
> Created_at: 2026-02-07 10:30:22 UTC  
> Updated_at: 2026-02-07 10:31:03 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2777403608  

This sould be made generic and passed by the user (with a default arg) as a [URGB&&](https://en.cppreference.com/w/cpp/named_req/UniformRandomBitGenerator.html).

> Username: Becheler  
> Created_at: 2026-02-09 15:22:58 UTC  
> Updated_at: 2026-02-09 15:22:59 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2783243575  

Done 👍🏽


---

## Review 9 [Commented]

> Username: joaquintides  
> Created_at: 2026-02-07 10:34:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3766868651  

📁 include/boost/graph/louvain_quality_functions.hpp

```diff
  70 |+ // k_c = sum of degrees in community c
  71 |+ // m = total edge weight / 2
  72 |+ struct newman_and_girvan
```

> Username: joaquintides  
> Created_at: 2026-02-07 10:34:17 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2777406168  

Is this modularity thing a general concept or does it apply to Louvain only?

> Username: Becheler  
> Created_at: 2026-02-09 14:41:58 UTC  
> Updated_at: 2026-02-09 14:43:05 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2783023259  

This is a yes and no situation.  
- The modularity can be used outside of louvain to assess partition quality of a graph.  
- But the current implementation with incremental computations (`remove`, `insert`, `gain`) is particularly suited for Louvain.  
- Making it generally useful would require to disentangle the two aspects.   
- But I would rather do it in a `clustering` folder so we can have:  
```  
include/boost/graph/clustering/  
├── quality_functions.hpp        # 10 incremental metrics/criterions for gen-louvain  
├── label_propagation.hpp      # another clustering method (future work)  
├── leiden.hpp                          # Leiden algorithm (future work)  
├── louvain.hpp                        # Louvain algorithm  
├── girvan_newman.hpp         # Edge betweenness clustering (currently in bc_clustering.hpp)  
```


---

## Review 10 [Changes requested]

> Username: jeremy-murphy  
> Created_at: 2026-02-08 10:12:29 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3768959805  

Couple more requests, still going...

📁 include/boost/graph/louvain_clustering.hpp

```diff
 131 |+ // Track hierarchy of aggregation levels for unfolding partitions.
 132 |+ template <typename VertexDescriptor>
 133 |+ struct hierarchy_t
```

> Username: jeremy-murphy  
> Created_at: 2026-02-08 06:15:10 UTC  
> Updated_at: 2026-02-08 10:12:29 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2778650944  

Only one data member and no real invariants to speak off suggests that you don't really need this type.   
I think `unfold` could be a free function, maybe in a private namespace depending on how reusable it is, that takes `levels`  and final_partition as parameters.  
  
By the way, please only use the `_t` suffix for type aliases, not for names of classes.

> Username: Becheler  
> Created_at: 2026-02-17 12:04:40 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2816702396  

Done 👍🏽


---

## Review 11 [Commented]

> Username: joaquintides  
> Created_at: 2026-02-20 10:39:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3831325281  

📁 doc/louvain_clustering.html

```diff
 117 |+ </blockquote>
 118 |+ 
 119 |+ IN: <tt>URBG&amp;&amp; gen</tt>
```

> Username: joaquintides  
> Created_at: 2026-02-20 10:39:16 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2832546521  

Would it make sense to provide a default arg for this?

> Username: Becheler  
> Created_at: 2026-02-20 11:05:10 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2832662657  

I am not sure what it would be. And also it would differ from what i've seen in `random.hpp` utilities:  
https://github.com/boostorg/graph/blob/3131c24630e42c79b43c1f32558041c219ab84b8/include/boost/graph/random.hpp


---

## Review 12 [Commented]

> Username: joaquintides  
> Created_at: 2026-02-20 10:40:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3831329585  

📁 doc/louvain_clustering.html

```diff
 125 |+ </blockquote>
 126 |+ 
 127 |+ IN: <tt>weight_type min_improvement_inner</tt>
```

> Username: joaquintides  
> Created_at: 2026-02-20 10:40:24 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2832550741  

Is Louvain guaranteed to converge (i.e. to eventually stop)? If this is not clear, does it make sense to provide additional hard limits on the number of inner/outer iterations?

> Username: Becheler  
> Created_at: 2026-02-20 11:15:38 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2832711494  

If I understand well it's guaranteed to terminate in theory:  
- the quality (modularity) is monotically non-decreasing through the algorithm  
- becasue the algorithm is supposed to only accept nodes moves and community merges that strictly improve (or does not decrease in some variations) the quality of the partition  
- modularity is bounded <=1  
- the number of partition is finite  
  
That being said there is the case of large graphs and the trouble on floating point precision.  
  
- For very large graphs maybe it would make sense to have some sense of async task: "please dear louvain, aggregate this for some time and when I'm done with waiting give me the last aggreagated graph you had". But that sounds like a very different interface ?  
- The current API is still more flexible than igraph and genlouvain, that do not offer parametrization of the stopping condition (igraph has 0 for inner and outer thresholds and genlouvain has 10-6, fixed)  
  
Am i making sense ?

> Username: jeremy-murphy  
> Created_at: 2026-02-22 22:48:24 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2838609551  

### Incremental output  
  
There is a general interface for keeping only the last value, but it's not a pattern I've actually ever seen in practice.  
  
Instead of returning a single value at the end (either by return value or in/out parameter), emit values by output iterator as they are calculated. Now, normally values emitted by output iterator are all kept by pushing back on a vector for example. But with a "clobbering" output iterator that always writes to the same location, the user can choose to just keep the last value. Neat, huh?  
  
However, that kind of updating output value is only necessary if we do actually allow the user to specify an early termination condition. (Which, by the way, is orthogonal to asynchrony. At least for now, until we start supporting C++20 and start returning futures from algorithms. If the user wants to run the algorithm in the background, they use `std::async`.) Usually that kind of user-specified condition should come in the form of a callback predicate, so the user can either use number of iterations or time elapsed and it is opaque to the algorithm. The algorithm would provide a default predicate that does whatever is the most reasonable thing to do, which might be applying no early termination condition at all.  
### Floating-point  
This is a huge topic, but in short we don't want to let floating-point accuracy cause our algorithm to run indefinitely. The basics are a) using a robust summation algorithm such as Kahan's, which is in Boost.Accumulators, and ... I can't remember what else.  :) Looking through the calculations to find subtractions that could be between two extremely close values. I might be able to provide more ideas later.


---

## Review 13 [Commented]

> Username: joaquintides  
> Created_at: 2026-02-20 10:41:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3831334322  

📁 doc/louvain_clustering.html

```diff
  87 |+ <H3>Parameters</H3>
  88 |+ 
  89 |+ IN: <tt>const Graph&amp; g</tt>
```

> Username: joaquintides  
> Created_at: 2026-02-20 10:41:30 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2832555066  

The algorithm has the additional requirement that vertices are copyable, hashable etc., as they're internally stored in unordered_sets.

> Username: Becheler  
> Created_at: 2026-02-20 11:18:07 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2832722879  

You're right. I have been changing the vertices handling in this aspect because it was not friendly with some types of graphs. The interface now takes a VertexIndexMap but I still have to commit those changes, sorry 😓   
I will update the documentation in that sense once I merged the new stuff


---

## Review 14 [Commented]

> Username: joaquintides  
> Created_at: 2026-02-20 11:16:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3831527369  

📁 include/boost/graph/louvain_clustering.hpp

```diff
  28 |+ #include <boost/container_hash/hash.hpp>
  29 |+ #include <algorithm>
  30 |+ #include <iostream>
```

> Username: joaquintides  
> Created_at: 2026-02-20 11:16:46 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2832716823  

Is this `#include` needed?

> Username: Becheler  
> Created_at: 2026-02-20 11:18:52 UTC  
> Updated_at: 2026-02-20 11:18:53 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2832726196  

Hem. Nope 😄

> Username: Becheler  
> Created_at: 2026-02-23 14:58:35 UTC  
> Updated_at: 2026-02-23 14:58:36 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2841331139  

done :)


---

## Review 15 [Commented]

> Username: joaquintides  
> Created_at: 2026-02-20 11:21:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3831550206  

📁 include/boost/graph/louvain_clustering.hpp

```diff
  31 |+ 
  32 |+ // Hash specialization for std::pair to use with boost::unordered containers
  33 |+ namespace std {
```

> Username: joaquintides  
> Created_at: 2026-02-20 11:21:50 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2832737402  

Big no-no :-)  
* It's forbidden to specialize hash for types other than user-defined ones, which `std::pair` is not.  
* Boost.Unordered does not use `std::hash` by default, but `boost::hash`. Your `temp_edge_weights` works because `boost::hash` works off the shelf with pairs, so the `std` specialization is not even used.

> Username: Becheler  
> Created_at: 2026-02-20 11:23:48 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2832745312  

Oooooh 🥲 Thank you, will remove

> Username: Becheler  
> Created_at: 2026-02-23 14:58:24 UTC  
> Updated_at: 2026-02-23 14:58:25 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2841330101  

done :)


---

## Review 16 [Commented]

> Username: joaquintides  
> Created_at: 2026-02-20 11:25:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3831567293  

📁 include/boost/graph/louvain_clustering.hpp

```diff
 365 |+ }
 366 |+ 
 367 |+ /// @brief Fast version, requires the QualityFunction to implement GraphPartitionQualityFunctionIncrementalConcept
```

> Username: joaquintides  
> Created_at: 2026-02-20 11:25:59 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2832753353  

Looks like this comment is wrong, this version does _not_ require the quality function to be incremental.

> Username: Becheler  
> Created_at: 2026-02-23 14:56:06 UTC  
> Updated_at: 2026-02-23 14:56:07 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2841317740  

fixed :)


---

## Review 17 [Changes requested]

> Username: jeremy-murphy  
> Created_at: 2026-02-22 21:36:09 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/graph/pull/453#pullrequestreview-3772760587  

📁 include/boost/graph/louvain_clustering.hpp

```diff
 123 |+         edge_descriptor e;
 124 |+         bool inserted;
 125 |+         tie(e, inserted) = add_edge(kv.first.first, kv.first.second, kv.second, new_g);
```

> Username: jeremy-murphy  
> Created_at: 2026-02-09 11:26:36 UTC  
> Updated_at: 2026-02-22 21:36:09 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2782105143  

If we're getting the result of `add_edge`, then presumably we should assert that the edge was inserted or something.

---

📁 include/boost/graph/louvain_clustering.hpp

```diff
 512 |+ /// @brief Find the best partition of the vertices of a graph conditionally to a quality function like modularity.
 513 |+ /// @return the modularity value of the best partition.
 514 |+ template <typename QualityFunction = newman_and_girvan, typename Graph, typename ComponentMap, typename WeightMap, typename URBG>
```

> Username: jeremy-murphy  
> Created_at: 2026-02-21 22:10:40 UTC  
> Updated_at: 2026-02-22 21:36:09 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2836721722  

I'll have to dig into why, but the convention in C++ is to pass callbacks as objects, not types, and use `operator()`. I.e.:  
  
```  
template <typename Graph, typename BinaryFunction>  
void foo(Graph g, BinaryFunction f)  
{  
  // ...  
  f(g);  
  // ...  
}  
```  
  
And then, as I think the rest of BGL follows, you would choose a default value with  
```  
template <typename Graph>  
foo(Graph g)  
{  
  return foo(g, your_default);  
}  
  
```

> Username: jeremy-murphy  
> Created_at: 2026-02-22 21:27:55 UTC  
> Updated_at: 2026-02-22 21:36:09 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2838528407  

I'm not sure there is fundamental reason why, but basically because `std::function` is the archetypal callback that can wrap anything, that's why "an object with `operator()`" is the convention.

> Username: Becheler  
> Created_at: 2026-02-23 08:04:10 UTC  
> Updated_at: 2026-02-23 08:04:11 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2839516990  

Yes I also wondered about making the qualities functors. I guess because copiable + callable is the kind of minimal constraint one can think about. Just to be clear, are you asking about change or are you still evaluating it ? :)

> Username: jeremy-murphy  
> Created_at: 2026-02-23 09:00:34 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2839717246  

Oh yes, you should change it to match the convention.

> Username: Becheler  
> Created_at: 2026-02-23 11:59:16 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2840485399  

Although I agree, one setback is that there is one more argument to pass to the function, making defaulting values tricky as more and more are added :)

> Username: jeremy-murphy  
> Created_at: 2026-02-23 20:46:59 UTC  
> Url: https://github.com/boostorg/graph/pull/453#discussion_r2843017495  

We'll find a way. ;)


---
