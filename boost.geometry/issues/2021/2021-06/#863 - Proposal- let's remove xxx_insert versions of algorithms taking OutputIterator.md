# #863 - Proposal: let's remove xxx_insert versions of algorithms taking OutputIterator [Open]

> Username: awulkiew  
> Created at: 2021-06-08 00:26:04 UTC  
> Updated at: 2021-06-16 09:20:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/863  

The algorithms:  
- `convex_hull_insert`  
- `intersection_insert`  
- `difference_insert`  
- `simplify_insert`  
- `sym_difference_insert`  
- `union_insert`  
  
The main reason is that they take nonintuitive parameters, e.g. `intersection_insert` takes OutputIterator and in addition to this SingleGeometry because an iterator doesn't hold the necessary information for the algorithm to work. Another example is `convex_hull_insert` which takes the compile-time information Closure and Order from the input Geometry because the iterator doesn't hold it which can result e.g. in taking it from a Box while filling some unrelated output Range/Ring/Polygon with points.  
  
Furthermore, they've been hidden in `detail` namespace so probably nobody uses them. But they have to be maintained.  
  
What do you think?

---

## Comment 1

> Username: barendgehrels  
> Created at: 2021-06-16 09:20:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/863#issuecomment-862198907  

Fine for me.  
  
If I remember correctly, we put them in detail years ago because we're not sure yet about their usefulness.   
  
So they can be removed now.
