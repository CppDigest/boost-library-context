# #847 Correct compute::context::get_devices method [Open]

> Username: JablonskiMateusz  
> Created at: 2020-06-24 12:54:40 UTC  
> Updated at: 2020-11-09 13:07:15 UTC  
> Url: https://github.com/boostorg/compute/pull/847  

get a vector of cl_device_id and then use the device ids  
to populate a vector of compute::device  
  
Signed-off-by: Mateusz Jablonski <mateusz.jablonski@intel.com>

---

## Comment 1

> Username: coveralls  
> Created_at: 2020-06-24 16:43:20 UTC  
> Updated_at: 2020-07-08 15:33:07 UTC  
> Url: https://github.com/boostorg/compute/pull/847#issuecomment-648934525  

[![Coverage Status](https://coveralls.io/builds/31941922/badge)](https://coveralls.io/builds/31941922)  
  
Coverage increased (+0.008%) to 84.027% when pulling **881ca4231de0b85b986b3e6a19949edcdf6340ec on JablonskiMateusz:master** into **36c89134d4013b2e5e45bc55656a18bd6141995a on boostorg:master**.

---

## Comment 2

> Username: JablonskiMateusz  
> Created_at: 2020-07-08 17:42:19 UTC  
> Url: https://github.com/boostorg/compute/pull/847#issuecomment-655661410  

@jszuppe could you look at this PR?

---

## Comment 3

> Username: kylelutz  
> Created_at: 2020-07-08 17:53:09 UTC  
> Url: https://github.com/boostorg/compute/pull/847#issuecomment-655666849  

Hey @JablonskiMateusz! Could you provide some detail on the motivation for this change? Was this causing issues with your OpenCL library?  
  
For context, `device` is a very thin wrapper on `cl_device_id`, and they have identical memory layouts. There should be no need to first populate `vector<cl_device_id>` and then copy into `vector<device>`.

---

## Comment 4

> Username: JablonskiMateusz  
> Created_at: 2020-07-09 08:06:49 UTC  
> Url: https://github.com/boostorg/compute/pull/847#issuecomment-655975662  

Hi @kylelutz,   
the problem is scenario with sub devices. The current implementation is that boost queries number of context devices, then creates a vector of `compute::device` (default constructor of `compute::device` is called, without calling _clRetainDevice_), then it passes the pointer to data of the vector to _clGetContextInfo_ and it fills the devices (also without calling _clRetainDevice_) and the vector is passed to the user's app. When the app destroys the vector, then the destructor of `compute::device` checks if it is a sub device and it calls _clReleaseDevice_. This causes that boost calls _clReleaseDevice_ without calling _clRetainDevice_ before. According to OpenCL spec, it caues UB:   
```  
After the device reference count becomes zero and all the objects attached to device (such as  
command-queues) are released, the device object is deleted. Using this function to release a  
reference that was not obtained by creating the object or by calling clRetainDevice causes  
undefined behavior.  
```  
  
IMHO the `cl_device_id` should be passed to `compute::device` to its constructor or to assignment operator, currently it is performed in a quite hacky way like memcpy to the memory of `compute::device` .  
  
My change was inspired by `compute::program::get_devices` where the vector of `compute::device` is populated from vector of `cl_device_id` https://github.com/boostorg/compute/blob/master/include/boost/compute/program.hpp#L191

---

## Comment 5

> Username: JablonskiMateusz  
> Created_at: 2020-07-14 06:20:07 UTC  
> Url: https://github.com/boostorg/compute/pull/847#issuecomment-657992566  

@kylelutz @jszuppe  any comments?

---

## Comment 6

> Username: keryell  
> Created_at: 2020-07-14 18:39:38 UTC  
> Url: https://github.com/boostorg/compute/pull/847#issuecomment-658345899  

I think that in a real application the `compute::context::get_devices` should not be the critical path, so having a more correct version does not seem like a problem.

---

## Comment 7

> Username: JablonskiMateusz  
> Created_at: 2020-07-27 06:51:13 UTC  
> Url: https://github.com/boostorg/compute/pull/847#issuecomment-664155005  

@kylelutz ping

---

## Comment 8

> Username: JablonskiMateusz  
> Created_at: 2020-08-20 15:03:53 UTC  
> Url: https://github.com/boostorg/compute/pull/847#issuecomment-677720803  

@kylelutz @jszuppe  ping

---

## Comment 9

> Username: JablonskiMateusz  
> Created_at: 2020-10-07 10:13:48 UTC  
> Url: https://github.com/boostorg/compute/pull/847#issuecomment-704837993  

kindly reminder @kylelutz

---

## Comment 10

> Username: JablonskiMateusz  
> Created_at: 2020-11-09 13:07:14 UTC  
> Url: https://github.com/boostorg/compute/pull/847#issuecomment-724000898  

@kylelutz @jszuppe could you merge this PR?

---
