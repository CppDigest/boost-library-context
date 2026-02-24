# #362 - Add mapped_view::map_async() method [Open]

> Username: kylelutz  
> Created at: 2014-12-29 18:10:52 UTC  
> Updated at: 2017-03-21 04:14:47 UTC  
> Url: https://github.com/boostorg/compute/issues/362  

Add a `map_async()` method to `mapped_view` which will perform a non-blocking call to `clEnqueueMapBuffer()`.  
##
