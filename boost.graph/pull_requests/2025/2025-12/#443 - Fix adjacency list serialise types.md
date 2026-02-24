# #443 Fix adjacency list serialise types [Open]

> Username: andreacassioli  
> Created at: 2025-12-05 21:23:00 UTC  
> Updated at: 2026-02-20 20:29:22 UTC  
> Url: https://github.com/boostorg/graph/pull/443  

Try to address [this ](https://github.com/boostorg/graph/pull/291) in a more general way.  
  
According to [the offical docs](https://www.boost.org/doc/libs/latest/libs/graph/doc/graph_concepts.html) the type representing the number of vertices and edges in a graph can be deduced by the graph traits.  
  
IMHO there are two approaches:  
1. read/write a fixed integer type to allow different graph types (with potentially different size types) to be used  
2. read/write integers that depend on the graph type, loosing potentially some fleibility  
  
I believe the former is the original design. So in this PR  
  
* make sure all read/write serializations are down using `unsigned int`  
* convert the relevant values to the type deduced from the graph traits  
* small cleanup modernizing a bit the code

---

## Comment 1

> Username: andreacassioli  
> Created_at: 2025-12-28 21:05:35 UTC  
> Url: https://github.com/boostorg/graph/pull/443#issuecomment-3695048501  

I think this might break compatibility because the size of the integers written and read will depends on the graphs used to read and write. So I believe there is a bit more to do.

---

## Comment 2

> Username: andreacassioli  
> Created_at: 2025-12-30 15:46:02 UTC  
> Url: https://github.com/boostorg/graph/pull/443#issuecomment-3699757568  

@jeremy-murphy there is a failure in the CI that I do not understand, it does seem to be related to my changes. What do you think?

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2025-12-30 22:10:26 UTC  
> Url: https://github.com/boostorg/graph/pull/443#issuecomment-3700662093  

> @jeremy-murphy there is a failure in the CI that I do not understand, it does seem to be related to my changes. What do you think?  
  
Yeah, that's very odd. Looks like a problem either in Boost.Math or in the system libraries themselves. I would just ignore it for now and hope it goes away by itself.

---

## Review 4 [Commented]

> Username: jeremy-murphy  
> Created_at: 2025-12-30 22:18:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/443#pullrequestreview-3619411948  

📁 include/boost/graph/adj_list_serialize.hpp

```diff
  46 |+         using Graph = adjacency_list< OEL, VL, D, VP, EP, GP, EL >;
  47 |+         using Vertex = typename graph_traits< Graph >::vertex_descriptor;
  48 |+         using SerializedInteger = unsigned int;
```

> Username: jeremy-murphy  
> Created_at: 2025-12-30 22:18:08 UTC  
> Url: https://github.com/boostorg/graph/pull/443#discussion_r2654003731  

Isn't this going to cause a problem for users with, however unlikely, graphs where `vertices_size_type` is `std::size_t` and actually have billions or trillions or vertices?  
  
Also, it would waste space for users that have billions of tiny graphs where they have customized `vertices_size_type` to be `unsigned char`, etc?

> Username: andreacassioli  
> Created_at: 2026-01-04 20:30:39 UTC  
> Updated_at: 2026-01-04 20:30:40 UTC  
> Url: https://github.com/boostorg/graph/pull/443#discussion_r2659910219  

yeah, I follow the current implementation approach of storing always the same type (i.e. int) so that one can read/write with different graph type. It is indeed a bit wasteful.   
  
Otherwise one could deduce these types fro the traits, assuming that the graph type remains the same. It was actually my first thought.

> Username: andreacassioli  
> Created_at: 2026-02-20 20:29:22 UTC  
> Url: https://github.com/boostorg/graph/pull/443#discussion_r2834985231  

I gave it another shot. Now I assume types are deduced from the graph.


---
