# #395 Apply std::remove_const before passing Graph to graph_traits in filtered_graph [Open]

> Username: tsnorri  
> Created at: 2024-11-08 10:52:29 UTC  
> Updated at: 2024-11-11 09:05:12 UTC  
> Url: https://github.com/boostorg/graph/pull/395  

For instance `detail::residual_graph` explicitly applies const to the graph. Consequently graph_traits would have to be defined also for the const graph type, but [the LEDA graph example](https://www.boost.org/doc/libs/1_86_0/libs/graph/doc/leda_conversion.html) does not seem to indicate that it should be needed.

---

## Comment 1

> Username: tsnorri  
> Created_at: 2024-11-08 10:53:32 UTC  
> Updated_at: 2024-11-08 10:53:51 UTC  
> Url: https://github.com/boostorg/graph/pull/395#issuecomment-2464399494  

I just noticed that the language version in use seems to be C++14. I can change the instances of `std::remove_const` to `std::remove_const_t` if needed.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2024-11-10 23:13:14 UTC  
> Url: https://github.com/boostorg/graph/pull/395#issuecomment-2466980685  

Hmmm, I would only do this if I'm 100% sure that the underlying graph can never actually be `const`, and it feels hacky and wrong. Funnily enough, there is a comment at the top of `filtered_graph_base`:  
  
> // This base class is a stupid hack to change overload resolution  
> // rules for the source and target functions so that they are a  
> // worse match than the source and target functions defined for  
> // pairs in graph_traits.hpp. I feel dirty. -JGS  
  
I would much rather someone redesigned `filtered_graph` to work elegantly, which might be much easier now with C++14 metaprogramming.

---

## Comment 3

> Username: tsnorri  
> Created_at: 2024-11-11 08:25:00 UTC  
> Updated_at: 2024-11-11 08:27:06 UTC  
> Url: https://github.com/boostorg/graph/pull/395#issuecomment-2467517019  

The underlying graph is in fact const at least when using `cycle_canceling`, since it eventually calls [`residual_graph`](https://github.com/boostorg/graph/blob/f1e51a08578a648c27ef61c0581adb9e8440cac4/include/boost/graph/detail/augment.hpp#L20-L26) which applies const to the graph type. I think the reason why the function works with `adjacency_list` is that the default implementation of `graph_traits` (which is [here](https://github.com/boostorg/graph/blob/f1e51a08578a648c27ef61c0581adb9e8440cac4/include/boost/graph/graph_traits.hpp#L56-L79) I think) gets the types from the graph type, so it does not matter if it is const or not.  
  
(This has more to do with my other PR but anyway if the graph does not have a particular type, e.g. `in_edge_iterator`, the default implementation of `graph_traits` just adds `void`. For example this compiles:  
```  
namespace {  
    struct test  
    {  
        typedef int vertex_descriptor;  
        typedef int edge_descriptor;  
        typedef boost::directed_tag directed_category;  
        typedef boost::allow_parallel_edge_tag edge_parallel_category;  
        typedef boost::incidence_graph_tag traversal_category;  
        typedef std::size_t vertices_size_type;  
        typedef std::size_t edges_size_type;  
    };  
  
    typedef boost::graph_traits <test> traits;  
    static_assert(std::is_same_v <traits::in_edge_iterator, void>);  
}  
```  
)  
  
I don’t think I’m familiar enough with BGL to come up with a better design for `filtered_graph`, so would you have any advice for now to get my code to work? Should I specialise `graph_traits` for the const version of my custom graph type, too?

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2024-11-11 09:05:10 UTC  
> Url: https://github.com/boostorg/graph/pull/395#issuecomment-2467599685  

Yeah, I'm not sure, to be honest. I would have to spend a while looking at it first and I don't have time to.

---
