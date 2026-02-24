# #46 Include <algorithm> when BOOST_MOVE_USE_STANDARD_LIBRARY_MOVE is defined [Merged]

> Username: mkurdej  
> Created at: 2021-12-10 08:40:13 UTC  
> Updated at: 2021-12-26 21:07:36 UTC  
> Merged at: 2021-12-26 21:07:24 UTC  
> Closed at: 2021-12-26 21:07:24 UTC  
> Url: https://github.com/boostorg/move/pull/46  

When directly using <boost/move/algo/move.hpp> without including it through <boost/move/move.hpp> (which includes <algorithm>), one stumbles upon the error like this one:  
```  
In file included from boost/boost/container/flat_set.hpp:29:  
In file included from boost/boost/container/detail/flat_tree.hpp:30:  
In file included from boost/boost/container/vector.hpp:60:  
In file included from boost/boost/move/algo/adaptive_merge.hpp:16:  
In file included from boost/boost/move/algo/detail/adaptive_sort_merge.hpp:48:  
boost/boost/move/algo/move.hpp:91:10: error: no member named 'move_backward' in namespace 'std'; did you mean 'container::move_backward'?  
using ::std::move_backward;  
      ^~~~~~~  
boost/boost/container/detail/copy_move_algo.hpp:893:4: note: 'container::move_backward' declared here  
move_backward(I f, I l, F r)  
^  
```  
  
Another solution would be to include <boost/move/algorithm.hpp>, but it's unnecessary.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2021-12-26 21:07:36 UTC  
> Url: https://github.com/boostorg/move/pull/46#issuecomment-1001242122  

Many thanks for the patch!

---
