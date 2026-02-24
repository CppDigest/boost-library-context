# #983 - get_turns does not work for const points [Closed]

> Username: barendgehrels  
> Created at: 2022-03-02 12:58:17 UTC  
> Updated at: 2022-04-07 12:10:22 UTC  
> Closed at: 2022-03-09 11:15:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/983  

The `get_turns` algorithm, used in set_operations, buffer and many relational operations, does not compile with const points, because it uses the `point_type` of their passed geometries for (now deprecated) robust recalculations.  
It can use the point-type of the turns (which should always be writable) instead.
