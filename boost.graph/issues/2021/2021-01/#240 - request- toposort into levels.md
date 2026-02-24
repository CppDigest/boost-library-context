# #240 - request: toposort into levels [Open]

> Username: joemalle  
> Created at: 2021-01-09 03:41:20 UTC  
> Updated at: 2021-01-09 03:41:20 UTC  
> Url: https://github.com/boostorg/graph/issues/240  

I don't think it's currently possible to get each "level" from`topological_sort` (where a level contains the set of vertices with no unfinished dependencies e.g. if a depends on b, b depends on z, and c depends on z, then the levels are [z], [b, c], [a]).  
  
I think this would be a helpful feature. I'd be happy to write a PR if others are interested.
