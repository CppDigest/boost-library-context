# #227 - maximum_weighted_matching Only Works on Select Filtered Graphs [Closed]

> Username: bradleykohler96  
> Created at: 2020-08-31 13:43:41 UTC  
> Updated at: 2023-05-30 03:08:39 UTC  
> Closed at: 2023-05-30 03:08:39 UTC  
> Url: https://github.com/boostorg/graph/issues/227  

I noticed a problem with maximum_weighted_matching on filtered graphs.  
I made a public Gist demonstrating the issue here:  
https://gist.github.com/studentbrad/4c932a589404639701dca75766c1078f  
  
What I noticed is that when you filter out vertices that are at the end of the `vecS` (i.e. vertex `7` in my case) the algorithm completes without error.  
However, when you filter out vertices that are not at the end of the `vecS` (i.e vertices `0`, `1`, `2`, `3`, `4`, `5` and `6` in my case) the algorithm results in a Segmentation Fault.  
  
![Screenshot from 2020-08-31 09-42-51](https://user-images.githubusercontent.com/15698049/91726513-60221e00-eb6e-11ea-8143-8ed3058013b7.png)

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2022-07-12 06:42:29 UTC  
> Url: https://github.com/boostorg/graph/issues/227#issuecomment-1181377735  

@yi-ji

---

## Comment 2

> Username: yi-ji  
> Created at: 2023-05-30 01:41:27 UTC  
> Url: https://github.com/boostorg/graph/issues/227#issuecomment-1567655331  

@studentbrad Thank you for using the algorithm!  
I can reproduce the issue by your Gist example. The issue is caused by the use of `filtered_graph`.  
  
Unfortunately, `filtered_graph` cannot be used for the maximum weighted matching algorithm, as [commented by the author](https://github.com/boostorg/graph/blob/develop/include/boost/graph/filtered_graph.hpp#L332) in `boost/graph/filtered_graph.hpp`:  
  
> // An alternative for num_vertices() and num_edges() would be to  
> // count the number in the filtered graph. This is problematic  
> // because of the interaction with the vertex indices...  they would  
> // no longer go from 0 to num_vertices(), which would cause trouble  
> // for algorithms allocating property storage in an array. We could  
> // try to create a mapping to new recalibrated indices, but I don't  
> // see an efficient way to do this.  
> //  
> // However, the current solution is still unsatisfactory because  
> // the following semantic constraints no longer hold:  
> // boost::tie(vi, viend) = vertices(g);  
> // assert(std::distance(vi, viend) == num_vertices(g));  
  
because the maximum weighted matching algorithm relies on both vertex iterators and `num_vertices` to create & maintain the vertices' label and property vectors (arrays).  
  
I would recommend to set the edge weights to minimum infinity instead of using filtered graphs, for example, in this [Gist](https://gist.github.com/yi-ji/4a060309803c991bf030dd2615caf2b6), the algorithm can solve your example correctly.  
Please let me know if you have any questions, or we can close this as not a bug. Thank you!

---

## Comment 3

> Username: bradleykohler96  
> Created at: 2023-05-30 03:08:38 UTC  
> Url: https://github.com/boostorg/graph/issues/227#issuecomment-1567700567  

@yi-ji Thanks for the response! I could have used this a long time ago 😉 As a follow up, I would recommend asserting, or throwing an exception. But, it seems that line already exists and is commented out - maybe for good reason.  
  
I will close the issue 😄 Cheers!
