# #112 - Watch out for ADL issues [Closed]

> Username: pfultz2  
> Created at: 2016-03-05 17:05:50 UTC  
> Updated at: 2016-03-23 18:42:12 UTC  
> Closed at: 2016-03-22 23:51:04 UTC  
> Url: https://github.com/boostorg/hof/issues/112  

There are several places where ADL can bite you in the library:  
  
always.hpp:121: always_base has boost::fit::detail as an associated namespace.   
  
apply_eval.hpp:90: return eval_ordered<R>(f, pack_join(...), ...)   
  
arg.hpp:86: make_args_at(...)(..., make_perfect_ref(...)...)   
  
arg.hpp:95: get_args<N>(...)
