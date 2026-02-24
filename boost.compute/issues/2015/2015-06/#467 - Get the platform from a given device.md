# #467 - Get the platform from a given device [Closed]

> Username: piotrwyderski  
> Created at: 2015-06-18 09:16:48 UTC  
> Updated at: 2015-08-11 05:04:01 UTC  
> Closed at: 2015-08-11 05:03:55 UTC  
> Url: https://github.com/boostorg/compute/issues/467  

There is no simple, user-accessible way to ask the default device  
about the platform it belongs to, i.e. the "default platform", despite  
the fact that the requested functionality is available, but defined  
in the private section, namely the device_platform() static method.  
The user it then required to effectively clone that method:  
  
default_device.get_info<cl_platform_id>(CL_DEVICE_PLATFORM)  
  
This code could be moved to the device itself, which would then allow  
the user to ask about the platform of any device, not just the default.

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-06-19 02:41:19 UTC  
> Url: https://github.com/boostorg/compute/issues/467#issuecomment-113348397  

There is already a [`device::platform()`](http://boostorg.github.io/compute/boost/compute/device.html#idp51956272-bb) method so you should be able to just do `boost::compute::system::default_device().platform()`. Or am I misunderstanding?
