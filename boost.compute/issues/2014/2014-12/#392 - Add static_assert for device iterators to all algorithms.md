# #392 - Add static_assert for device iterators to all algorithms [Open]

> Username: kylelutz  
> Created at: 2014-12-29 21:15:13 UTC  
> Updated at: 2017-03-21 04:14:50 UTC  
> Url: https://github.com/boostorg/compute/issues/392  

Add `static_assert`'s for `is_device_iterator` to all algorithms which require device iterators. This will help prevent errors (or improve the error messages) when using these algorithms with host iterators.  
##
