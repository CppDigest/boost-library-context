# #324 - Sparse Array for Group By [Open]

> Username: ricejasonf  
> Created at: 2017-03-05 07:58:52 UTC  
> Updated at: 2017-07-03 04:02:28 UTC  
> Url: https://github.com/boostorg/hana/issues/324  

This is just a thought, but we should consider making a sparse array similar to `hana::detail::hash_table` for the purpose of making a fast "group by" algorithm. One problem would be that it would have to use `fold_left` to construct the lookup which I recall was an expensive up front cost.  
  
The result could even be a `hana::map` as it appears that everything except `make_map` is agnostic to the use of `hana::hash`.  
  
I kind of got a start on something. The following is ripped from `hana::detail::hash`:  
  
```cpp  
namespace sparse_array  
{  
  template <typename Key, int ...i>  
  struct group { };  
  
  template <typename ...Groups>  
  struct lookup_table  
    : Groups...  
  { };  
  
  // find indices  
  template <typename Key, std::size_t ...i>  
  std::index_sequence<i...> find_indices_impl(group<Key, i...> const&);  
  
  template <typename Key>  
  std::index_sequence<> find_indices_impl(...);  
  
  template <typename Lookup, typename Key>  
  struct find_indices {  
    using type = decltype(find_indices_impl<Key>(Lookup{}));  
  };  
  
  // update_group  
  template <typename Group, typename Key, std::size_t Index>  
  struct update_group {  
    using type = Group;  
  };  
  
  template <std::size_t ...i, typename Key, std::size_t Index>  
  struct update_group<group<Key, i...>, Key, Index> {  
    using type = group<Key, i..., Index>;  
  };  
  
  // group_insert  
  template <typename Lookup, typename Node, std::size_t Index, bool =  
    (find_indices<Lookup, typename Node::key>::type::size() > 0)  
  >  
  struct group_insert;  
  
  template <typename ...Groups, typename Node, std::size_t Index>  
  struct group_insert<lookup_table<Groups...>, Node, Index, true> {  
    // There is a group for that Key; append the new index to it.  
    using type = lookup_table<  
      typename update_group<Groups, typename Node::key, Index>::type...  
    >;  
  };  
  
  template <typename ...Groups, typename Node, std::size_t Index>  
  struct group_insert<lookup_table<Groups...>, Node, Index, false> {  
    // There is no group for that Key; insert a new group.  
    using type = lookup_table<Groups..., group<typename Node::key, Index>>;  
  };  
  
  template <typename Xs>  
  struct make {  
    using type = typename decltype(  
      hana::fold_left(  
        std::declval<Xs>(),  
        hana::type_c<lookup_table<>>,  
        hana::template_<group_insert>  
      )  
    )::type;  
  };  
}  
```  
  
See the following for background information:  
http://stackoverflow.com/questions/42541148/how-do-i-speed-up-many-different-hanafilters-on-the-same-input-data

---

## Comment 1

> Username: badair  
> Created at: 2017-07-03 04:00:28 UTC  
> Updated at: 2017-07-03 04:02:28 UTC  
> Url: https://github.com/boostorg/hana/issues/324#issuecomment-312544751  

This would be super useful for turning an edge list...  
```  
hana::tuple<  
    hana::pair<A, B>,  
    hana::pair<A, C>,  
    hana::pair<A, D>,  
    hana::pair<B, C>,  
    hana::pair<B, D>>  
```  
...into an adjacency list for a compile-time graph:  
```  
hana::tuple<  
    hana::pair<A, hana::tuple<B, C, D>>,  
    hana::pair<B, hana::tuple<C, D>>>  
```  
  
(assuming I understand the intent of @ricejasonf's post)
