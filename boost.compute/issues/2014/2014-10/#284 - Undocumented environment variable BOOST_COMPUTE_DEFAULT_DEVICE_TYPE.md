# #284 - Undocumented environment variable BOOST_COMPUTE_DEFAULT_DEVICE_TYPE [Closed]

> Username: WojciechMula  
> Created at: 2014-10-24 11:10:08 UTC  
> Updated at: 2014-10-25 15:41:09 UTC  
> Closed at: 2014-10-25 15:41:09 UTC  
> Url: https://github.com/boostorg/compute/issues/284  

This variable is read in system::find_default_device and we've found it very useful. Maybe this var should be documented?

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-10-24 13:07:32 UTC  
> Url: https://github.com/boostorg/compute/issues/284#issuecomment-60384013  

Oops, looks like I forgot that one. Fixed in PR #285. Thanks!
