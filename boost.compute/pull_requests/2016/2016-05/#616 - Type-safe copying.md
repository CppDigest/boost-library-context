# #616 Type-safe copying [Merged]

> Username: jszuppe  
> Created at: 2016-05-26 21:34:52 UTC  
> Updated at: 2016-06-01 16:37:47 UTC  
> Merged at: 2016-06-01 04:26:41 UTC  
> Closed at: 2016-06-01 04:26:41 UTC  
> Url: https://github.com/boostorg/compute/pull/616  

Resolves https://github.com/boostorg/compute/issues/363.  
#### Sync. copying to/from device  
  
For both coping data to and from device there are 3 `dispatch_copy()` functions and each function works in different scenario:  
1. Copying to device when value types (of `InputIterator` and `OutputIterator`) match and host iterator is contiguous  
2. Copying to device when value types mismatch and host iterator is contiguous  
3. Copying to device when host iterator is not contiguous (values types does not have to match)  
##### 1st option  
  
It's straightforward copying using OpenCL functions for reading and writing a device memory.  
##### 2nd option has three sub-options  
1. Maping device memory to host and performing `std::copy` (casting/converting and copying on host)  
2. Using `std::vector<DeviceIterator::value_type>` as a intermediate memory  (casting/converting on host, copying using OpenCL function)  
3. Maping host memory to the device and using copy kernel to both convert and copy the data.  
##### 3rd option has two sub-options  
1. Maping device memory to host and performing `std::copy` (casting/converting and copying on host)  
2. Using `std::vector<DeviceIterator::value_type>` as a intermediate memory  (casting/converting on host, copying using OpenCL function)  
##### Additional info  
  
When copying to host memory we treat host iterators whose value type is `bool` as if they were non-contiguous.  
#### Async. copying to/from device  
  
There is asynchronous copying with mismatching types works too. In both cases (to/from device) I used maping host memory to the device and using copy kernel to both convert and copy the data.  
#### Various fixes  
- Fix types in `perf_sort_by_key.cpp`  
- Fix return values in async coping `svm_ptr<>` from/to device  
- Fix test in test_functional_as.cpp

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-05-26 22:55:59 UTC  
> Url: https://github.com/boostorg/compute/pull/616#issuecomment-222018116  

[![Coverage Status](https://coveralls.io/builds/6353471/badge)](https://coveralls.io/builds/6353471)  
  
Coverage increased (+0.3%) to 80.828% when pulling **c3f32bee50c3b66705dc8197b15cf099fac36adb on haahh:pr_typesafe_copy** into **d41ff00c06945c927418395491c8ed82787c0a4b on boostorg:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-05-28 16:24:36 UTC  
> Updated_at: 2016-05-28 21:03:22 UTC  
> Url: https://github.com/boostorg/compute/pull/616#discussion_r64990961  

We should probably just remove this define from the file. It should only really be defined by the user when they are debugging.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2016-05-28 16:33:27 UTC  
> Updated_at: 2016-05-28 21:03:22 UTC  
> Url: https://github.com/boostorg/compute/pull/616#discussion_r64991044  

Will fix that later today.

---

## Comment 4

> Username: coveralls  
> Created_at: 2016-05-28 16:41:13 UTC  
> Url: https://github.com/boostorg/compute/pull/616#issuecomment-222317608  

[![Coverage Status](https://coveralls.io/builds/6373490/badge)](https://coveralls.io/builds/6373490)  
  
Coverage increased (+0.3%) to 80.857% when pulling **69e09f27d8337ae5f5ec26742efcc318ca42e90c on haahh:pr_typesafe_copy** into **d41ff00c06945c927418395491c8ed82787c0a4b on boostorg:develop**.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2016-05-28 16:41:14 UTC  
> Updated_at: 2016-05-28 21:03:22 UTC  
> Url: https://github.com/boostorg/compute/pull/616#discussion_r64991110  

Shouldn't `asynchronously` here be `synchronously`? Also, I think an alternative would be instead of mapping and unmapping the buffer, replace the call above with `copy_on_device_async()` and wait on the returned event/future.

---

## Comment 6

> Username: jszuppe  
> Created_at: 2016-05-28 16:47:55 UTC  
> Updated_at: 2016-05-28 21:03:22 UTC  
> Url: https://github.com/boostorg/compute/pull/616#discussion_r64991202  

Yes, indeed, it should be `synchronously` since later `wait()` is called for event returned by unmap operation.   
  
And yes, those lines can be replace by just calling `copy_on_device_async()` as it does the same (create buffer for mapping host memory, perform copying on device and map/unmap buffer).

---

## Comment 7

> Username: kylelutz  
> Created_at: 2016-05-28 16:50:10 UTC  
> Updated_at: 2016-05-28 21:03:22 UTC  
> Url: https://github.com/boostorg/compute/pull/616#discussion_r64991229  

Can you change this to `unmap_event` to match the Boost style?

---

## Comment 8

> Username: kylelutz  
> Created_at: 2016-05-28 16:58:53 UTC  
> Updated_at: 2016-05-28 21:03:22 UTC  
> Url: https://github.com/boostorg/compute/pull/616#discussion_r64991322  

Good catch :-).

---

## Comment 9

> Username: kylelutz  
> Created_at: 2016-05-28 16:59:41 UTC  
> Updated_at: 2016-05-28 16:59:53 UTC  
> Url: https://github.com/boostorg/compute/pull/616#issuecomment-222318496  

Looks good! Left a few comments. I'll finish up reviewing/testing this out today.

---

## Comment 10

> Username: jszuppe  
> Created_at: 2016-05-28 19:42:41 UTC  
> Updated_at: 2016-05-28 19:43:30 UTC  
> Url: https://github.com/boostorg/compute/pull/616#issuecomment-222326222  

Thanks! Just fyi, I ran all the tests with SVM on Windows with the latest AMD drivers with fixed `clEnqueueSVMMemcpy()` (you can see that there are tests with `svm_ptr<>` that are skipped for AMD, this is the reason).

---

## Comment 11

> Username: jszuppe  
> Created_at: 2016-05-28 19:49:59 UTC  
> Updated_at: 2016-05-28 21:03:22 UTC  
> Url: https://github.com/boostorg/compute/pull/616#discussion_r64993199  

Fix: `dispatch_copy_async()`, not `copy_on_device_async()`. We have to map and unmap at the end to force OpenCL driver to update host memory (mapping is a synchronization point).

---

## Comment 12

> Username: jszuppe  
> Created_at: 2016-05-28 20:30:11 UTC  
> Url: https://github.com/boostorg/compute/pull/616#issuecomment-222328316  

I've just found that that there is an inconsistency in what async. copy functions return when `first == last`.  In one function it's future object with `result` iterator and an empty event (`event()`), in other functions it's empty future object... However, from user perspective it does not matter because it's impossible to get the value in both of those cases (user gets `Invalid event` error or sth like this when try to `get()` the value).

---

## Comment 13

> Username: coveralls  
> Created_at: 2016-05-28 22:24:10 UTC  
> Url: https://github.com/boostorg/compute/pull/616#issuecomment-222332476  

[![Coverage Status](https://coveralls.io/builds/6375059/badge)](https://coveralls.io/builds/6375059)  
  
Coverage increased (+0.3%) to 80.861% when pulling **8f4db3d7b7d86f4e6894d74dce5f40d6944c1277 on haahh:pr_typesafe_copy** into **d41ff00c06945c927418395491c8ed82787c0a4b on boostorg:develop**.

---

## Comment 14

> Username: kylelutz  
> Created_at: 2016-06-01 04:26:42 UTC  
> Url: https://github.com/boostorg/compute/pull/616#issuecomment-222889427  

Awesome! Merged!

---
