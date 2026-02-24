# #431 Lazy initialization for `astar_search` [Open]

> Username: fei0319  
> Created at: 2025-05-19 09:09:49 UTC  
> Updated at: 2025-10-07 22:50:28 UTC  
> Url: https://github.com/boostorg/graph/pull/431  

This PR fixes #356.  
  
The current `astar_search` implementation does initialization for all nodes in the graph, which is unnecessary and expensive given that in most cases we only access a small part of the graph. This PR ensures that only the nodes actually explored by the search are initialized, eliminating unnecessary overhead for unused nodes.  
  
This PR will add a new constraint on the input color map: `get(color_map, uninitialized_node)` must return `white_color`. The default value, `std::vector` and `std::(unordered_)map` satisfy this constraint, so it should be generally fine.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2025-05-19 12:26:46 UTC  
> Url: https://github.com/boostorg/graph/pull/431#issuecomment-2890826421  

Even a small change in semantics would require changes to unit tests and documentation. If it doesn't break any existing unit tests, then we definitely need to add some that demonstrate the actual semantics.

---

## Comment 2

> Username: fei0319  
> Created_at: 2025-05-19 12:48:47 UTC  
> Url: https://github.com/boostorg/graph/pull/431#issuecomment-2890889742  

Thanks, I will add some unit tests for that, if we don't turn to the following way.  
  
We have another choice that won't change the semantics though. The reason why we need the color map initialized properly is that the underlying `breadth_first_search` use color to determine whether a node has been visited before, and therefore a uninitialized color map will crush the `breath_first_search`; if we use a `std::unordered_set` or something like that to maintain the visited node set manually, we can properly lazy initialize color map like other maps.  
  
The only problem is that I am not sure whether all vertex types can be a key in `std::unordered_set` ... It seems sometimes `vertex_descriptor` can be non-hashable. Or should we use something else than `std::unordered_set`? Could you leave some suggestions?

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2025-05-19 13:09:15 UTC  
> Url: https://github.com/boostorg/graph/pull/431#issuecomment-2890978467  

Thanks for raising the design questions early before committing to one. I'll have to reacquaint myself with the existing design, but I feel there must be a way to specify a default value for a property map.  
We certainly wouldn't lock ourselves in to using `std::unordered_set`.

---

## Comment 4

> Username: fei0319  
> Created_at: 2025-05-20 04:53:42 UTC  
> Url: https://github.com/boostorg/graph/pull/431#issuecomment-2892953534  

I fixed a bug that crushed the tests. It should be fine now.  
  
I believe that a full initialization of the color map is necessary, if we are not to assume that it has `white_color` default values. After all, the PropertyMap concept is a rather weak restriction, only providing `get` and `put`... I fail to figure out how we can use `get` and `put` to ensure the default values, except performing `put(u, white_color)` on all nodes.  
  
Given that in most cases users pass in a properly defaulted color map or simply use the default color map, I think such a restriction is acceptable, as long as we make it clear in the doc. Additionally, I suggest that we should use a `associative_property_map` rather than a `shared_array_property_map` as the default color map, since the latter one performs  a full initialization.  
  
Do you think this is good? Or do you know a better way out?

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2025-05-20 23:42:23 UTC  
> Url: https://github.com/boostorg/graph/pull/431#issuecomment-2896070836  

I think the key here is `operator[]`, but I have to confirm.

---
