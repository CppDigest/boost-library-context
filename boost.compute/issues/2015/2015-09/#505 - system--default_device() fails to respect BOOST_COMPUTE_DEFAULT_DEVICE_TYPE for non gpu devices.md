# #505 - system::default_device() fails to respect BOOST_COMPUTE_DEFAULT_DEVICE_TYPE for non gpu devices [Closed]

> Username: pisto  
> Created at: 2015-09-08 14:32:44 UTC  
> Updated at: 2015-09-15 17:52:10 UTC  
> Closed at: 2015-09-15 17:52:04 UTC  
> Url: https://github.com/boostorg/compute/issues/505  

This is because the type of the device id is a bitmask, but the test used are equalities. This means that if the device type is cpu but it is also default, it's type will be `CL_DEVICE_TYPE_DEFAULT|CL_DEVICE_TYPE_CPU != boost::compute::device::cpu`.

---

## Comment 1

> Username: jszuppe  
> Created at: 2015-09-08 14:45:13 UTC  
> Url: https://github.com/boostorg/compute/issues/505#issuecomment-138588653  

What do you mean by that? You cannot set CPU as default device? Because for me `export BOOST_COMPUTE_DEFAULT_DEVICE_TYPE=CPU` works.   
  
Can you point the file and line where `device.type()` is compared to `boost::compute::device::cpu / gpu / accelerator` instead of `&`?

---

## Comment 2

> Username: jszuppe  
> Created at: 2015-09-08 14:48:15 UTC  
> Url: https://github.com/boostorg/compute/issues/505#issuecomment-138589753  

Did you mean this [include/boost/compute/system.hpp#L229](https://github.com/boostorg/compute/blob/master/include/boost/compute/system.hpp#L229)?

---

## Comment 3

> Username: pisto  
> Created at: 2015-09-08 14:49:14 UTC  
> Updated at: 2015-09-08 14:50:26 UTC  
> Url: https://github.com/boostorg/compute/issues/505#issuecomment-138590027  

It may work for you if your CPU device is not the default one. I don't know how to mark system-wide my devices as defaults. Anway, I can find these occurrencies:  
  
```  
include/boost/compute/system.hpp:229:                    if (device.type() != device::gpu)  
include/boost/compute/system.hpp:233:                    if (device.type() != device::cpu)  
include/boost/compute/system.hpp:250:            if(device.type() == device::gpu){  
include/boost/compute/system.hpp:259:            if(device.type() == device::cpu){  
test/test_command_queue.cpp:166:       device.type() == boost::compute::device::gpu){  
```  
  
I suggest you clear CL_DEVICE_TYPE_DEFAULT inside device::type() here https://github.com/boostorg/compute/blob/master/include/boost/compute/device.hpp#L158

---

## Comment 4

> Username: pisto  
> Created at: 2015-09-08 14:50:41 UTC  
> Updated at: 2015-09-08 14:50:46 UTC  
> Url: https://github.com/boostorg/compute/issues/505#issuecomment-138590416  

Updated comment a few times, see previous.

---

## Comment 5

> Username: jszuppe  
> Created at: 2015-09-08 14:52:01 UTC  
> Url: https://github.com/boostorg/compute/issues/505#issuecomment-138590811  

Aaaaa, OK, now I get it.

---

## Comment 6

> Username: jszuppe  
> Created at: 2015-09-08 15:12:04 UTC  
> Updated at: 2015-09-08 15:30:54 UTC  
> Url: https://github.com/boostorg/compute/issues/505#issuecomment-138596096  

> I suggest you clear CL_DEVICE_TYPE_DEFAULT inside device::type() here > https://github.com/boostorg/compute/blob/master/include/boost/compute/device.hpp#L158  
  
I think there's not need for that. We just need to change `if (device.type() != device::gpu)` to  `if (!(device.type() & device::gpu))` (and so on), like you did in system.hpp:  
  
```  
include/boost/compute/system.hpp:229:                    if (!(device.type() & device::gpu))  
include/boost/compute/system.hpp:233:                    if (!(device.type() & device::cpu))  
include/boost/compute/system.hpp:250:            if(device.type() & device::gpu){  
include/boost/compute/system.hpp:259:            if(device.type() & device::cpu){  
```  
  
`bool(CL_DEVICE_TYPE_DEFAULT|CL_DEVICE_TYPE_CPU) & device::cpu)` should give true.

---

## Comment 7

> Username: jszuppe  
> Created at: 2015-09-08 15:34:47 UTC  
> Url: https://github.com/boostorg/compute/issues/505#issuecomment-138602178  

You can make pull request with these changes (in `system.hpp` and you should also change `device.type() == boost::compute::device::gpu` to `device.type() & boost::compute::device::gpu` in `test/test_command_queue.cpp:166`).

---

## Comment 8

> Username: jszuppe  
> Created at: 2015-09-13 20:24:22 UTC  
> Url: https://github.com/boostorg/compute/issues/505#issuecomment-139914531  

This can be closed, see https://github.com/boostorg/compute/pull/508.
