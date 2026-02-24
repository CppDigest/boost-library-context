# #58 - Allow custom argument names with BOOST_COMPUTE_FUNCTION() [Closed]

> Username: kylelutz  
> Created at: 2014-03-10 01:16:53 UTC  
> Updated at: 2014-04-21 02:24:00 UTC  
> Closed at: 2014-04-21 02:24:00 UTC  
> Url: https://github.com/boostorg/compute/issues/58  

Right now the `BOOST_COMPUTE_FUNCTION()` macro automatically generates argument names of the form `_1`, `_2`, ..., `_N`. It should be possible to assign meaningful names to function arguments instead of always requiring the use of the auto-generated names.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-04-21 02:24:00 UTC  
> Url: https://github.com/boostorg/compute/issues/58#issuecomment-40911954  

Implemented in 4b67907023a84265eb4b389cdd9d47e564e39422.
