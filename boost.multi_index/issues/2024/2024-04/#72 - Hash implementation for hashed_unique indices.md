# #72 - Hash implementation for hashed_unique indices? [Closed]

> Username: bgemmill  
> Created at: 2024-04-01 19:53:19 UTC  
> Updated at: 2024-04-03 14:59:17 UTC  
> Closed at: 2024-04-02 07:33:50 UTC  
> Url: https://github.com/boostorg/multi_index/issues/72  

What hashing implementation is boost::multi_index using? My apologies if this was obvious, but I couldn't find it from the [documentation](https://live.boost.org/doc/libs/1_84_0/libs/multi_index/doc/tutorial/indices.html#hashed_indices).  
  
I'm curious because there's been a large amount of performance work going into [boost::container's hashes](https://www.boost.org/doc/libs/1_84_0/libs/unordered/doc/html/unordered.html#changes_release_1_82_0_major_update) recently, and it would be interesting to see if that could be applied to multi_index as well.

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-04-02 07:33:50 UTC  
> Url: https://github.com/boostorg/multi_index/issues/72#issuecomment-2031277040  

Hi Ben, the internal data structure of Boost.MultiIndex hashed indices is different from those used in Boost.Unordered. For one, `boost::multi_index_container` is a node-based container, which makes it impossible to adopt the innovations introduced in [`boost::unordered_flat_map`](https://bannalia.blogspot.com/2022/11/inside-boostunorderedflatmap.html). As for `boost::unordered_map`, the data structures are also different:  
  
* [Description of boost::unordered_map](https://bannalia.blogspot.com/2022/06/advancing-state-of-art-for.html)  
* [Description of Boost.MultiIndex hashed indices](https://bannalia.blogspot.com/2014/01/a-better-hash-table.html)  
  
You can find a benchmark comparing both data structures [here](https://www.boost.org/libs/unordered/doc/html/unordered.html#benchmarks_boostunordered_multiset). `boost::unordered_[map|set]` is generally faster than Boost.MultiIndex, while `boost::unordered_multi[map|set]` performs worse on lookup.  
  
There are several reasons that would prevent Boost.MultiIndex to use the approaches followed by Boost.Unordered, the most salient one being that Boost.MultiIndex iterators are guaranteed to remain stable even after rehashing, which is not the case for containers in Boost.Unordered.

---

## Comment 2

> Username: bgemmill  
> Created at: 2024-04-02 12:53:51 UTC  
> Url: https://github.com/boostorg/multi_index/issues/72#issuecomment-2031967163  

Thanks for the reply!  
  
I was thinking specifically of [boost::unordered_node_map](https://www.boost.org/doc/libs/1_84_0/libs/unordered/doc/html/unordered.html#unordered_node_map), which is both node based and has stable iterators; that may be more interesting than the flat family of containers.  
  
There may be other reasons why that won't work as well, but it seemed worth bringing up.

---

## Comment 3

> Username: joaquintides  
> Created at: 2024-04-02 15:03:04 UTC  
> Url: https://github.com/boostorg/multi_index/issues/72#issuecomment-2032299622  

`boost::unordered_node_map` does not preserve iterator stability upon rehashing, either.

---

## Comment 4

> Username: bgemmill  
> Created at: 2024-04-02 15:18:09 UTC  
> Updated at: 2024-04-02 15:19:07 UTC  
> Url: https://github.com/boostorg/multi_index/issues/72#issuecomment-2032343943  

Thanks again for the reply, this may be a documentation issue for unordered_node_map.  
  
Looking [here](https://www.boost.org/doc/libs/1_84_0/libs/unordered/doc/html/unordered.html#unordered_node_map) I see:  
```boost::unordered_node_map uses an open-addressing layout like boost::unordered_flat_map, but, being node-based, it provides pointer/iterator stability and node handling functionalities. ```  
  
And [here](https://www.boost.org/doc/libs/1_84_0/libs/unordered/doc/html/unordered.html#regular_iterator_invalidation):  
```As for pointers and references, they are never invalidated for node-based containers (boost::unordered_[multi]set, boost::unordered_[multi]map, boost::unordered_node_set, boost::unordered_node_map), but they will be when rehashing occurs for boost::unordered_flat_set and boost::unordered_flat_map```  
  
If the internals don't actually maintain iterator stability I can file a separate bug with unordered.  
  
edit: I can spell

---

## Comment 5

> Username: joaquintides  
> Created at: 2024-04-02 15:40:58 UTC  
> Url: https://github.com/boostorg/multi_index/issues/72#issuecomment-2032410652  

> `boost::unordered_node_map` uses an open-addressing layout like `boost::unordered_flat_map`, but, being node-based, it provides pointer/iterator stability and node handling functionalities.   
  
Yes, this an error in the documentation, pointer stability is preserved always, but iterator stability only when no rehashing occurs. It should have been "pointer/reference stability". Would you mind filing an issue so that I don't forget about fixing it? Thank you!  
  
> As for pointers and references, they are never invalidated for node-based containers (`boost::unordered_[multi]set`, `boost::unordered_[multi]map`, `boost::unordered_node_set`, `boost::unordered_node_map`), but they will be when rehashing occurs for `boost::unordered_flat_se`t and `boost::unordered_flat_map`  
  
This is correct: `boost::unordered_node_map` provides **pointer/reference** stability no matter what. Note that this does not include iterators, for which there's an explanation one paragraph above:  
  
> [...] Iterators can be invalidated by calls to insert, rehash and reserve.

---

## Comment 6

> Username: bgemmill  
> Created at: 2024-04-03 14:59:16 UTC  
> Url: https://github.com/boostorg/multi_index/issues/72#issuecomment-2034859076  

Done, thanks for your attention here.
