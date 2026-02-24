# #457 - undocumented filtered graph helpers? [Open]

> Username: andrea-cassioli-maersk  
> Created at: 2026-02-16 10:15:15 UTC  
> Updated at: 2026-02-18 13:05:35 UTC  
> Url: https://github.com/boostorg/graph/issues/457  

Hi,  
I stumbled across some helpers in `filter_graph.hpp` for which I cannot find any docs:  
  
- `make_vertex_subset_filter`  
- `make_vertex_subset_compliment_filter`  
  
and the corresponding filters  
  
- `is_in_subset`  
- `is_not_in_subset`  
  
Are they supposed to be exposed by the library?

---

## Comment 1

> Username: Becheler  
> Created at: 2026-02-16 11:56:09 UTC  
> Url: https://github.com/boostorg/graph/issues/457#issuecomment-3908079183  

Hi @andrea-cassioli-maersk  !  
  
Thank you for this report 👍🏽   
  
For context, they're public convenience utilities located in `<boost/graph/filtered_graph.hpp>` :https://github.com/boostorg/graph/blob/develop/include/boost/graph/filtered_graph.hpp   
  
The git history shows that they were written by Jeremy Siek (the BGL author) in 2001, as part of the original filtered_graph implementation. They were always in the `boost::` namespace, never in `detail::` (so de facto considered public I guess). In 2010 Jeremiah Willcock found the typo `make_vertex_subset_compliment_filter` but explicitly preserved the old spelling for backwards compatibility and added a note saying to use the correct one `make_vertex_subset_compliment_filter`. I wonder at what point that could be removed (since we bumped to C++14 anyway).  
  
Let's ask @jeremy-murphy  but I believe they are intentionally exposed helpers but undocumented because priority in early BGL was given to high-level algorithms and data structures, not trivial helpers like those.

---

## Comment 2

> Username: andrea-cassioli-maersk  
> Created at: 2026-02-16 12:14:31 UTC  
> Url: https://github.com/boostorg/graph/issues/457#issuecomment-3908159270  

Thank @Becheler .  
I also noticed the filters only support ordered sets, possibly because unordered sets where not in the stl at that time. If we keep the functionalities, it would be nice to extend the supported type (and maybe have same filters for edges?).

---

## Comment 3

> Username: Becheler  
> Created at: 2026-02-16 12:35:02 UTC  
> Url: https://github.com/boostorg/graph/issues/457#issuecomment-3908252058  

You're right, it looks like it supports:  
- `std::set`  
- `boost::unordered_set`  
  
But lacks support for:  
- `std::unordered_set`  
- `boost::unordered_flat_set`  
- `boost::container_flat_set`  
- generic containers with `.find()`, `.count()`, `.contains()`  
  
Although I am not sure about the trade-off between code volume / utility for users. Unless I am mistaken, this is a one liner with modern C++14 lambdas:  
  
```cpp  
auto g2 = make_vertex_subset_filter(graph, set);  
```  
versus:  
```cpp  
auto g2 = filtered_graph(g, keep_all(), [&s](auto v){ return s.count(v); });  
```

---

## Comment 4

> Username: andrea-cassioli-maersk  
> Created at: 2026-02-16 13:59:34 UTC  
> Url: https://github.com/boostorg/graph/issues/457#issuecomment-3908648086  

I agree, a lambda is enough! So maybe the question is whether they should either documented, or removed altogether (not sure if deprecation is a thing).

---

## Comment 5

> Username: Becheler  
> Created at: 2026-02-16 14:04:25 UTC  
> Updated at: 2026-02-16 14:05:36 UTC  
> Url: https://github.com/boostorg/graph/issues/457#issuecomment-3908672773  

That looks like it could probably be removed/replaced with lambdas. They have 0 external usage outside of `filtered_graph.hpp`. Since they are undocumented, I doubt users heavily rely on them.   
  
Maybe mark them as BOOST_DEPRECATED for one release, then remove them ? 🤔

---

## Comment 6

> Username: jeremy-murphy  
> Created at: 2026-02-18 12:24:21 UTC  
> Url: https://github.com/boostorg/graph/issues/457#issuecomment-3920541959  

I will try to have a look tomorrow.

---

## Comment 7

> Username: jeremy-murphy  
> Created at: 2026-02-18 12:31:22 UTC  
> Url: https://github.com/boostorg/graph/issues/457#issuecomment-3920574268  

Maybe I'm being old-fashioned, but sometimes it's nice to have names for things. Like, if lots of users are going to write the same lambda for a very common use case, then it probably deserves a name.

---

## Comment 8

> Username: Becheler  
> Created at: 2026-02-18 12:37:43 UTC  
> Updated at: 2026-02-18 12:38:21 UTC  
> Url: https://github.com/boostorg/graph/issues/457#issuecomment-3920603092  

I don't know. If we add `make_vertex_subset_filter`, the logical next request is `make_vertex_property_filter`, `make_edge_label_filter`, `make_degree_filter`, etc. and suddenly we have  15 variations of `filtered_graph` to document and maintain 😅   
  
It seems like a tradeoff between API surface/discoverability and naming. It seems the STL algorithms solves this by defaulting to the most common use case, not coming up with new names like `sort_less`, `sort_greater` ?  
  
Also if this is truly internal, maybe it's not a big deal :)

---

## Comment 9

> Username: jeremy-murphy  
> Created at: 2026-02-18 12:59:04 UTC  
> Url: https://github.com/boostorg/graph/issues/457#issuecomment-3920701842  

Yes, I essentially agree and I realised I wasn't specific enough. It's not the existing names that I think are worthwhile, but the lambdas.

---

## Comment 10

> Username: Becheler  
> Created at: 2026-02-18 13:04:39 UTC  
> Updated at: 2026-02-18 13:05:35 UTC  
> Url: https://github.com/boostorg/graph/issues/457#issuecomment-3920728537  

ooooh apologies for misunderstanding, you meant that like `std::less` you could see a predicate family like `boost::graph::label_in(set)`  ?
