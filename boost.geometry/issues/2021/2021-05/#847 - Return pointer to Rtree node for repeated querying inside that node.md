# #847 - Return pointer to Rtree node for repeated querying inside that node? [Closed]

> Username: Lenostatos  
> Created at: 2021-05-06 16:16:56 UTC  
> Updated at: 2021-09-28 22:44:02 UTC  
> Closed at: 2021-09-28 22:44:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/847  

Hi,  
  
I have data sets that cover larger areas but my algorithm (mean shift) performs multiple queries within only a small part of that area before it moves on to another spot.  
  
Thus, I was thinking that maybe the performance could be improved if I could get a pointer to the Rtree node that covers that smaller sub-area and then perform the local queries only on that node.  
  
I'm not entirely sure whether that is a meaningful approach, so I would love to hear your thoughts about it!  
  
Regarding an API for that, I'm not sure what it should look like. Maybe there could be a function that let's me query my Rtree object with a bounding box and returns another Rtree(-like) object that internally just points at the nodes which intersect with that bounding box. Or maybe one could set up an Rtree to remember such a node internally and check it first as long as spatial queries stay within the node's bbox.  
  
My specific use case involves airborne lidar point clouds, i.e. data sets of commonly millions of points that model the surface of areas ranging from multiple hectares to some km². Point densities lie between 0.5 to 100 points per m². I use a variant of the mean shift algorithm to identify individual tree crowns in these point clouds.  
  
Thanks a lot!  
Leon

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-05-06 19:18:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/847#issuecomment-833795110  

I'm not sure if R-tree is the best structure for that. I'd guess that kd-tree, esspecially a kd-tree with links between neighbouring nodes for speeding up traversing close areas would be better. AFAIR this was specifically designed for LIDAR point clouds, because when a new LIDAR measurement has to be merged with the existing point cloud with an algorithm like Iterative Closest Points the points are typically close to each other.  
  
----  
  
But if you want to use the R-tree, the most simple approach would probably be to construct small R-tree from the large one:  
`bgi::rtree<...>small(large | bgi::queried(bgi::intersects(box)));`  
This way the `large` R-tree is traversed to get all of the elements intersecting a `box` and the `small` R-tree is constructed using packing algorithm. This might be slower than you need but might as well be sufficiently fast. It probably depends how much work do you need to do in the small area and how much smaller it is WRT the rest.  
  
----  
  
If that's not what you're looking for, want to traverse the R-tree locally by yourself and store some node for further traversal you can do it using the `view` allowing you to access the internalls of the R-tree. Have in mind that this is not a part of the official interface. Have a look here:  
https://github.com/boostorg/geometry/tree/develop/include/boost/geometry/index/detail/rtree/utilities  
These are some utilities that are not implemented inside the R-tree but instead are using the `view` in order to access private members of the R-tree. See e.g.:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/rtree/utilities/are_boxes_ok.hpp  
in particular:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/rtree/utilities/are_boxes_ok.hpp#L118-L131  
  
As you can see the tree is traversed using visitor pattern so you have to write your own visitor for getting the desired internal node and another for subsequent queries. Or for the latter use the existing query visitor:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/rtree/visitors/spatial_query.hpp#L22-L96  
  
----  
  
Then you could build on top of that and keep the state of traversing the R-tree as a stack of nodes (depth first search stack). This way you could go up and down the tree at any point if needed in case you wanted to process some different area that was close to the current one. This way you wouldn't be forced to traverse the tree from the top again.  
  
Stack of nodes is stored in query iterators. This is probably more complex than what you need because your algorithm doesn't have to have the structure of an iterator. But you can get a general idea from this code. This is the visitor keeping the stack of nodes (actually it's a stack of ranges, current and end iterators of internal nodes that will be traversed) and traversing the tree until the next element meeing spatial predicate is found:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/rtree/visitors/spatial_query.hpp#L98-L249  
It is used by this iterator:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/rtree/query_iterators.hpp#L66-L135
