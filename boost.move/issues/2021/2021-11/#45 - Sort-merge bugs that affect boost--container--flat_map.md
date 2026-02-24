# #45 - Sort/merge bugs that affect boost::container::flat_map [Closed]

> Username: igaztanaga  
> Created at: 2021-11-03 14:36:53 UTC  
> Updated at: 2021-11-03 14:45:06 UTC  
> Closed at: 2021-11-03 14:45:06 UTC  
> Url: https://github.com/boostorg/move/issues/45  

As explained in https://github.com/boostorg/container/issues/198, there is are a couple of issues in Boost.Move's algorithms that cause failures in boost::container::flat_map. The issues are:  
  
- Missing a case in "adaptive_sort_build_params", when collected keys are less that 2*l_intbuf but higher than "l_intbuf+ n_min_ideal_keys".  
- Missing "return" statement in "merge_adaptive_ONlogN_recursive".
