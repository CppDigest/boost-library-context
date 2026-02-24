# #82 - Function definition in header file [Closed]

> Username: jbouny  
> Created at: 2018-08-29 08:58:02 UTC  
> Updated at: 2018-08-29 20:20:18 UTC  
> Closed at: 2018-08-29 20:20:18 UTC  
> Url: https://github.com/boostorg/container/issues/82  

Hello,  
  
With the last fix/refactoring commit on adaptive pool (04b0791593a2a87e8a4fb906b1d5cc65ffc2610d), I have an "already defined symbol" issue when building our software with boost 1.68.  
  
Indeed, when including `boost/container/adaptive_pool.hpp`, it includes `boost/container/detail/adaptive_node_pool.hpp` which itself includes `boost/container/detail/adaptive_node_pool_impl.hpp`.  
  
The issue in in this last file, where we are declaring AND defining the function `boost::container::dtl::candidate_power_of_2_rt()`. This triggers a nice linker error.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-08-29 20:20:18 UTC  
> Url: https://github.com/boostorg/container/issues/82#issuecomment-417092118  

Many thanks for the report, fixed in commit:  
  
https://github.com/boostorg/container/commit/d4ff624264cf157bae67e9161fadd247759be79d
