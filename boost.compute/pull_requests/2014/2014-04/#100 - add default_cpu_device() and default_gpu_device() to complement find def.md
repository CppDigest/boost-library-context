# #100 add default_cpu_device() and default_gpu_device() to complement find def... [Closed]

> Username: ghost  
> Created at: 2014-04-23 21:14:07 UTC  
> Updated at: 2014-06-14 09:42:13 UTC  
> Closed at: 2014-06-03 05:44:09 UTC  
> Url: https://github.com/boostorg/compute/pull/100  

add default_cpu_device() and default_gpu_device() to complement find default_device()  
  
I found myself often wanting to quickly compare things on my cpu vs. my gpu, the best way to do this (that I could think of) was to know ahead of time what your device name was and use find device.  
  
I think this is a good solution for this but would love some feedback.  
  
thanks  
  
Dan

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-04-23 21:38:54 UTC  
> Url: https://github.com/boostorg/compute/pull/100#issuecomment-41218224  

[![Coverage Status](https://coveralls.io/builds/707086/badge)](https://coveralls.io/builds/707086)  
  
Coverage remained the same when pulling **1130d5e4080d99665b3919dc1dd9049039e10ae0 on daniel-murray:master** into **127b3504112cd78723483213f37d5eb7bad6aa08 on kylelutz:master**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-04-24 05:10:19 UTC  
> Url: https://github.com/boostorg/compute/pull/100#issuecomment-41243247  

Thanks for working on this Daniel!  
  
Currently, the best way to change the default device is to set the `BOOST_COMPUTE_DEFAULT_DEVICE` environment variable to the name of the device you want to use (these can be printed out with the `list_devices` example). There is some documentation for that here: https://github.com/kylelutz/compute/wiki/Developer%27s-Guide#environment-variables  
  
If it's useful to you, we could probably add a `BOOST_COMPUTE_DEFAULT_DEVICE_TYPE` environment variable which takes a string like `GPU` or `CPU` and allows you to select a device based on the value returned from `device::type()`. That way we would avoid adding more functions to the API and allow the user to select devices based on environment variables rather than having them change and recompile their code. What do you think?

---

## Comment 3

> Username: ghost  
> Created_at: 2014-04-24 06:13:17 UTC  
> Url: https://github.com/boostorg/compute/pull/100#issuecomment-41245832  

ah, I missed that part of the documentation entirely. thats a much better way of doing it, `BOOST_COMPUTE_DEFAULT_DEVICE_TYPE` sounds good, will that include ACCELERATOR or similar? I'm happy to work on this later today.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-04-24 06:22:08 UTC  
> Url: https://github.com/boostorg/compute/pull/100#issuecomment-41246248  

That would be great! And ya, `"GPU"`, `"CPU"` and `"ACCELERATOR"` should all work (and I'd make it case-insensitive just for ease of use). Let me know if you run into any issues.  
  
And yeah... that documentation from the wiki should probably be added to the doxygen documentation for the `system::default_device()` function.  
  
Also, one more note, for pull-requests in the future, can you make them against the `develop` branch? That's where we do the testing and integration before merging into the `master` branch. There's some docs on that in the [CONTRIBUTING.md](https://github.com/kylelutz/compute/blob/master/CONTRIBUTING.md) file. Thanks!

---

## Comment 5

> Username: ghost  
> Created_at: 2014-04-24 06:24:54 UTC  
> Url: https://github.com/boostorg/compute/pull/100#issuecomment-41246401  

ok will do.

---

## Comment 6

> Username: kylelutz  
> Created_at: 2014-06-03 05:44:09 UTC  
> Url: https://github.com/boostorg/compute/pull/100#issuecomment-44922199  

Closing. See PR #128.

---
