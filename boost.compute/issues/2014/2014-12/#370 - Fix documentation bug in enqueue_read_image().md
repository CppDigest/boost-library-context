# #370 - Fix documentation bug in enqueue_read_image() [Open]

> Username: kylelutz  
> Created at: 2014-12-29 18:47:36 UTC  
> Updated at: 2017-03-21 04:14:52 UTC  
> Url: https://github.com/boostorg/compute/issues/370  

As reported during the Boost.Compute review, there is a bug when generating the documentation for the `command_queue::enqueue_read_image()` method and any other methods which accept a C-array (e.g. `const size_t array[2]`) as an argument. This looks to be an issue when converting the doxygen comments to quickbook.  
##
