# #667 Add OpenCL 2.0 command queue properties [Merged]

> Username: keryell  
> Created at: 2016-10-11 16:04:45 UTC  
> Updated at: 2016-11-08 04:10:58 UTC  
> Merged at: 2016-11-08 04:10:57 UTC  
> Closed at: 2016-11-08 04:10:57 UTC  
> Url: https://github.com/boostorg/compute/pull/667  

I wanted to play with device-side enqueue in OpenCL 2.x but it was not available in Boost.Compute.  
Here are the missing properties.  
  
On the longer term, do we want device command queue objects like the DeviceCommandQueue in https://github.com/KhronosGroup/OpenCL-CLHPP/blob/master/input_cl2.hpp#L8143 ?

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-10-11 16:32:52 UTC  
> Url: https://github.com/boostorg/compute/pull/667#issuecomment-252970849  

Thanks!  
  
> On the longer term, do we want device command queue objects like the DeviceCommandQueue in https://github.com/KhronosGroup/OpenCL-CLHPP/blob/master/input_cl2.hpp#L8143 ?  
  
That's a good idea since in `command_queue` you can't specify `CL_QUEUE_SIZE` property which user should be able to specify for a device command queue. (see [clCreateCommandQueueWithProperties](https://www.khronos.org/registry/cl/sdk/2.0/docs/man/xhtml/clCreateCommandQueueWithProperties.html)).

---

## Comment 2

> Username: jszuppe  
> Created_at: 2016-10-11 16:34:37 UTC  
> Url: https://github.com/boostorg/compute/pull/667#issuecomment-252971334  

Please change the base of the pull request to the `develop` branch.

---

## Comment 3

> Username: keryell  
> Created_at: 2016-10-11 18:08:00 UTC  
> Url: https://github.com/boostorg/compute/pull/667#issuecomment-252997232  

I rebased the branch onto develop.

---

## Comment 4

> Username: jszuppe  
> Created_at: 2016-10-11 18:13:37 UTC  
> Updated_at: 2016-10-11 18:20:34 UTC  
> Url: https://github.com/boostorg/compute/pull/667#issuecomment-252998809  

No, I didn't want you to rebase your branch onto develop :), but to change the base of this pull request.   
  
Under the title of the pull request there is:  
  
> keryell  wants to merge 2 commits into **boostorg:master** from keryell:ronan/2.0-queue-properties  
  
You need to edit the pull request and change base from master to develop ([tutorial](https://github.com/blog/2224-change-the-base-branch-of-a-pull-request)), that way your changes are merged into develop and not straight into master branch (see [Developer's-Guide: Submitting-patches](https://github.com/boostorg/compute/wiki/Developer's-Guide#submitting-patches))

---

## Comment 5

> Username: coveralls  
> Created_at: 2016-10-11 19:57:13 UTC  
> Url: https://github.com/boostorg/compute/pull/667#issuecomment-253027234  

[![Coverage Status](https://coveralls.io/builds/8288747/badge)](https://coveralls.io/builds/8288747)  
  
Coverage remained the same at 83.338% when pulling **20680cb367eb3f09bd85d9f354cd45fd622571aa on keryell:ronan/2.0-queue-properties** into **82f588f24ed2640bca4bf3f31dfcb06703d9bc13 on boostorg:master**.

---

## Comment 6

> Username: coveralls  
> Created_at: 2016-10-11 21:25:26 UTC  
> Url: https://github.com/boostorg/compute/pull/667#issuecomment-253051373  

[![Coverage Status](https://coveralls.io/builds/8290324/badge)](https://coveralls.io/builds/8290324)  
  
Coverage remained the same at 83.338% when pulling **20680cb367eb3f09bd85d9f354cd45fd622571aa on keryell:ronan/2.0-queue-properties** into **82f588f24ed2640bca4bf3f31dfcb06703d9bc13 on boostorg:master**.

---

## Comment 7

> Username: keryell  
> Created_at: 2016-10-18 07:55:01 UTC  
> Url: https://github.com/boostorg/compute/pull/667#issuecomment-254433923  

Sorry, I stopped somehow in the middle of the process. :-/

---

## Comment 8

> Username: kylelutz  
> Created_at: 2016-10-19 04:32:51 UTC  
> Url: https://github.com/boostorg/compute/pull/667#issuecomment-254707968  

Sorry, we had some issues with master and develop getting out of sync. Can you rebase on current master and/or remove the 10b216f9cd3d46f5d4c9ef1a6e328cf1e728bbc2 commit from this pull request?

---

## Comment 9

> Username: keryell  
> Created_at: 2016-11-07 21:15:57 UTC  
> Url: https://github.com/boostorg/compute/pull/667#issuecomment-258964917  

OK, I rebased my branch back to master, without the spurious commit. Is this correct now?

---

## Comment 10

> Username: coveralls  
> Created_at: 2016-11-08 01:08:47 UTC  
> Url: https://github.com/boostorg/compute/pull/667#issuecomment-259014928  

[![Coverage Status](https://coveralls.io/builds/8714933/badge)](https://coveralls.io/builds/8714933)  
  
Coverage remained the same at 83.338% when pulling **3be6b4e96c7f7bbaa40b35c6e1e1c579dd77d308 on keryell:ronan/2.0-queue-properties** into **82f588f24ed2640bca4bf3f31dfcb06703d9bc13 on boostorg:master**.

---

## Comment 11

> Username: kylelutz  
> Created_at: 2016-11-08 04:10:46 UTC  
> Url: https://github.com/boostorg/compute/pull/667#issuecomment-259040900  

Looks good! Thanks!

---
