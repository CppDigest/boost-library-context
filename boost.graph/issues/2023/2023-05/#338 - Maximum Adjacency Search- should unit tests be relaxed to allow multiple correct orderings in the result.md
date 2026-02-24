# #338 - Maximum Adjacency Search: should unit tests be relaxed to allow multiple correct orderings in the result? [Open]

> Username: jeremy-murphy  
> Created at: 2023-05-01 00:13:10 UTC  
> Updated at: 2023-05-01 00:13:10 UTC  
> Url: https://github.com/boostorg/graph/issues/338  

When @daankolthof added more tests to MAS, it occurred to me that different but equally correct implementations of the algorithm might return different orderings when tie-breaking edges with equal weights. I haven't confirmed that this is a real problem, it's just a suspicion.  
  
Assuming I'm right, the principle here is that unit tests should be written to test the implementation against the _specification_, not against itself.
