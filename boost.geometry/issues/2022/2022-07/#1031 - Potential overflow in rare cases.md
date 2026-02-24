# #1031 - Potential overflow in rare cases [Open]

> Username: barendgehrels  
> Created at: 2022-07-06 06:28:38 UTC  
> Updated at: 2022-07-06 06:28:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/1031  

See comments in #1028   
  
We have now:  
`promoted_type const dx = get<0>(p) - get<0>(vertex);`  
  
which could overflow if p is negative and huge, and vertex is positive and huge (or vv). But in buffer, that won't occur (coordinates of offset point and vertex are not that far apart).  
  
We could first convert them to promoted type. This functionality is shared anyway (buffer_side_straight, buffer_join_round_by_divide, buffer_join_miter) and specific for cartesian only. So we can move it to a separate function and fix it once there.
