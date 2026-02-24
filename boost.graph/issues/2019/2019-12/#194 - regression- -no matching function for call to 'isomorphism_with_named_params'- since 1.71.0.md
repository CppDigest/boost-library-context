# #194 - regression: "no matching function for call to 'isomorphism_with_named_params'" since 1.71.0 [Open]

> Username: mgehre  
> Created at: 2019-12-19 09:16:53 UTC  
> Updated at: 2019-12-19 09:36:34 UTC  
> Url: https://github.com/boostorg/graph/issues/194  

My code using `boost::graph::isomorphism(g1, g2)` works with boost 1.70.0 but fails to compile with boost 1.71.0. I'm using clang 9.  
  
For a reproducer, see https://godbolt.org/z/bysaYi
