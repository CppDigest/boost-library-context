# #118 - Non-unique inplace_set_difference used in in flat_tree_merge_unique and iterator invalidation in insert_unique [Closed]

> Username: igaztanaga  
> Created at: 2019-04-24 20:45:10 UTC  
> Updated at: 2019-04-24 20:50:19 UTC  
> Closed at: 2019-04-24 20:50:19 UTC  
> Url: https://github.com/boostorg/container/issues/118  

1) In "flat_tree_merge_unique" inplace_set_difference is used insted of inplace_set_unique_difference  
  
2) Im "insert_unique", the operation "seq.erase(e, seq.cend());" can invalidate the iterator "it" in the corner case if "it == e", so "it" should not be used in "flat_tree_container_inplace_merge".  This invalidation does not show in contiguous memory sequence containers like vector, but it is triggered when using stable_vector and other non-contiguous sequences.
