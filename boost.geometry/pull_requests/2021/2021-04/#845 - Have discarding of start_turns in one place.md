# #845 Have discarding of start_turns in one place [Merged]

> Username: barendgehrels  
> Created at: 2021-04-28 11:23:56 UTC  
> Updated at: 2021-05-19 07:28:03 UTC  
> Merged at: 2021-05-19 07:27:44 UTC  
> Closed at: 2021-05-19 07:27:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/845  

It also cleans up clusters, before assigning  
  
This brings the error rate in the recursive test back to 85 (from 181)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-04-28 11:24:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/845#pullrequestreview-646940271  

📁 include/boost/geometry/algorithms/detail/overlay/handle_colocations.hpp

```diff
 227 | 
 229 |- template<typename Turns, typename Clusters>
 230 |- inline void discard_start_turns(Turns& turns, Clusters& clusters)
```

> Username: barendgehrels  
> Created_at: 2021-04-28 11:24:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/845#discussion_r622081853  

This code is gone now


---

## Comment 2

> Username: barendgehrels  
> Created_at: 2021-05-12 08:21:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/845#issuecomment-839571817  

@awulkiew @vissarion OK to merge?

---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2021-05-12 08:42:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/845#pullrequestreview-657621041  

📁 include/boost/geometry/algorithms/detail/overlay/handle_colocations.hpp

```diff
 233 |-     {
 234 |-         cluster_info& cinfo = nv.second;
 235 |-         auto& indices = cinfo.turn_indices;
```

> Username: barendgehrels  
> Created_at: 2021-05-12 08:42:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/845#discussion_r630845509  

This (by chance) also fixes #835


---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2021-05-12 13:51:52 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/845#pullrequestreview-657913542  

I am OK.

---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2021-05-12 15:24:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/845#pullrequestreview-658027895  

📁 include/boost/geometry/algorithms/detail/overlay/discard_duplicate_turns.hpp

```diff
  97 |+     using multi_and_ring_id_type = std::pair<signed_size_type, signed_size_type>;
  98 |+ 
  99 |+     auto adapted_id_of = [](segment_identifier const& seg_id)
```

> Username: awulkiew  
> Created_at: 2021-05-12 15:24:23 UTC  
> Updated_at: 2021-05-12 15:24:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/845#discussion_r631144116  

This is a function so to should probably be called `adapt_id_of` or `to_multi_and_ring_id` ot something like that. But I'm ok with the current name if you don't feel to change it.

> Username: barendgehrels  
> Created_at: 2021-05-13 08:54:01 UTC  
> Updated_at: 2021-05-13 08:54:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/845#discussion_r631665833  

Makes sense. I will change it and then merge, thanks.


---

## Comment 6

> Username: awulkiew  
> Created_at: 2021-05-12 15:25:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/845#issuecomment-839863718  

@barendgehrels yes, thanks!

---
