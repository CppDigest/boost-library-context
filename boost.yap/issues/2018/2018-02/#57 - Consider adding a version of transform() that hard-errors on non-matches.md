# #57 - Consider adding a version of transform() that hard-errors on non-matches [Closed]

> Username: tzlaine  
> Created at: 2018-02-20 02:49:04 UTC  
> Updated at: 2018-06-11 17:16:20 UTC  
> Closed at: 2018-02-23 06:01:47 UTC  
> Url: https://github.com/boostorg/yap/issues/57  

In addition, there is another possible mode that has  
  better type safety which I will call:  
  4. manual: No default behavior.  If a node is not handled  
     explicitly, it is a hard compile error.  
  
But this isn't transform() at all, because it doesn't recurse.  It only matches the top-level expression, or you get a hard error.  Why not just write my_func() that takes a certain expression and returns another?  Calling it with the wrong type of expression will result your desired hard error.  Wait, is the use case that I think my transform matches all subexpressions within the top-level expression, and I want to verify that this is the case? I don't know how often that will come up.  I can't think of a single time I've written a Yap transform expecting it to match all nodes, except to evaluate it.  It could be useful in those cases now that I think of it...
