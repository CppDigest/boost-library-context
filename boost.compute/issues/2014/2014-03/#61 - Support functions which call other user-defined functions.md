# #61 - Support functions which call other user-defined functions [Open]

> Username: kylelutz  
> Created at: 2014-03-10 01:30:30 UTC  
> Updated at: 2019-06-23 11:01:36 UTC  
> Url: https://github.com/boostorg/compute/issues/61  

It should be possible for custom functions (e.g. those created with `BOOST_COMPUTE_FUNCTION()`) to call other user-defined functions.  
##

---

## Comment 1

> Username: rosenrodt  
> Created at: 2019-06-23 11:01:36 UTC  
> Url: https://github.com/boostorg/compute/issues/61#issuecomment-504741061  

I am also interested in having this feature too. Also it would be very helpful if one could create a host- & device-callable function via something like `BOOST_COMPUTE_HOST_DEVICE_FUNCTION()` that expands to regular C host function as well as `compute::function`.   
  
Then the `meta_kernel` class could somehow prepend the stringized device function based on information given by the user (via an extra parameter to `BOOST_COMPUTE_FUNCTION()`) or done automatically just by searching for matching function name
