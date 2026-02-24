# #179 Move device_ptr to the detail namespace [Merged]

> Username: kylelutz  
> Created at: 2014-07-09 04:05:12 UTC  
> Updated at: 2014-07-11 03:02:41 UTC  
> Merged at: 2014-07-11 03:02:39 UTC  
> Closed at: 2014-07-11 03:02:39 UTC  
> Url: https://github.com/boostorg/compute/pull/179  

This deprecates the device_ptr class and moves it to the detail  
namespace. The buffer_iterator class should be used instead of  
device_ptr for referencing a memory location on the device.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-07-09 04:30:47 UTC  
> Url: https://github.com/boostorg/compute/pull/179#issuecomment-48428905  

[![Coverage Status](https://coveralls.io/builds/947030/badge)](https://coveralls.io/builds/947030)  
  
Coverage remained the same when pulling **e0535d723379d33f7ef0cac4f258631eabd5e666 on deprecate-device-ptr** into **89089576ab6e02d589ccd4432f610bfc80916003 on develop**.

---
