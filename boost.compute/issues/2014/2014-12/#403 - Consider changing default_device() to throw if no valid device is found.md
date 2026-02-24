# #403 - Consider changing default_device() to throw if no valid device is found [Closed]

> Username: kylelutz  
> Created at: 2014-12-31 04:40:33 UTC  
> Updated at: 2015-02-26 23:02:56 UTC  
> Closed at: 2015-02-26 23:02:53 UTC  
> Url: https://github.com/boostorg/compute/issues/403  

Consider changing the `system::default_device()` method to throw some sort of "no device found" exception if no valid compute device is found. Currently it returns a null (default-constructed) `device` object. This can lead to issues if callers of `default_device()` don't check to ensure the device is valid.

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-02-07 20:23:55 UTC  
> Url: https://github.com/boostorg/compute/issues/403#issuecomment-73381296  

Implemented in PR #422.
