# #290 Add OpenCL version checks to tests [Closed]

> Username: kylelutz  
> Created at: 2014-11-04 04:25:02 UTC  
> Updated at: 2014-11-05 04:10:26 UTC  
> Closed at: 2014-11-05 04:10:15 UTC  
> Url: https://github.com/boostorg/compute/pull/290  



---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-11-04 04:26:34 UTC  
> Url: https://github.com/boostorg/compute/pull/290#issuecomment-61592015  

@shehzan10 Can you try this out on your NVIDIA machine?

---

## Comment 2

> Username: coveralls  
> Created_at: 2014-11-04 04:38:34 UTC  
> Url: https://github.com/boostorg/compute/pull/290#issuecomment-61592641  

[![Coverage Status](https://coveralls.io/builds/1423752/badge)](https://coveralls.io/builds/1423752)  
  
Coverage remained the same when pulling **85eb6b2f9b57d8a78e80b37d2ce7cf4e4fb3287d on more-version-checks** into **4c0c7bdde7c8b43462a326eb90aa105a3b403669 on develop**.

---

## Comment 3

> Username: shehzan10  
> Created_at: 2014-11-04 04:58:06 UTC  
> Url: https://github.com/boostorg/compute/pull/290#issuecomment-61593698  

In test_event, the tests (event callback, lambda callback, event_to_std_future) require OpenCL 1.2. That should fix it since NVIDIA supports only upto 1.1.  
Other than that, looks good on Intel and AMD devices.  
  
Quick note, I think black_scholes example is erroring out on Intel CPU only, although it gets the same result as on NVIDIA GPU. Not sure if this is only at my end. This happens on 2 different systems with 4770k cpu. Here's the output:  
  
```  
build(more-version-checks)$ BOOST_COMPUTE_DEFAULT_DEVICE_TYPE=CPU ./example/black_scholes  
device: Intel(R) Core(TM) i7-4770K CPU @ 3.50GHz  
option 0 call price: 0.0999002  
option 0 put price: 43.0524  
error: option prices are wrong  
[Exit 255 ]  
build(more-version-checks)$ ./example/black_scholes  
device: Quadro K5000  
option 0 call price: 0.0999  
option 0 put price: 43.0524  
```

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-11-04 05:07:32 UTC  
> Url: https://github.com/boostorg/compute/pull/290#issuecomment-61594188  

Do they require OpenCL 1.2 though? Most of the tests just call `clSetEventCallback()` which only requires OpenCL 1.1. What kind of failures are you seeing?  
  
For the black-scholes error, that just looks like a precision problem. Can you try changing the error tolerance from `1e-6` to `1e-3`?

---

## Comment 5

> Username: shehzan10  
> Created_at: 2014-11-04 05:20:10 UTC  
> Url: https://github.com/boostorg/compute/pull/290#issuecomment-61594910  

The error tolerance fixed it on the CPU side.  
  
It looks like the event callbacks may require OpenCL 1.2. Or it may be that the NVIDIA implementation is somehow different (not sure how valid this is).  
Output for each failure below.  
  
```  
build(more-version-checks)$ ./test/test_event  
Running 4 test cases...  
unknown location(0): fatal error in "event_callback": memory access violation at address: 0x00000000: no mapping at fault address  
/workspace/area51/compute/test/test_event.cpp(43): last checkpoint  
  
*** 1 failure detected in test suite "TestEvent"  
[Exit 201 ]  
  
build(more-version-checks)$ ./test/test_event  
Running 4 test cases...  
unknown location(0): fatal error in "lambda_callback": memory access violation at address: 0x00000000: no mapping at fault address  
/workspace/area51/compute/test/test_event.cpp(27): last checkpoint  
  
*** 1 failure detected in test suite "TestEvent"  
[Exit 201 ]  
  
build(more-version-checks)$ ./test/test_event  
Running 4 test cases...  
terminate called after throwing an instance of 'std::system_error'  
  what():  Unknown error -1  
unknown location(0): fatal error in "event_to_std_future": signal: SIGABRT (application abort requested)  
/workspace/area51/compute/test/test_event.cpp(27): last checkpoint  
  
*** 1 failure detected in test suite "TestEvent"  
[Exit 201 ]  
```

---

## Comment 6

> Username: kylelutz  
> Created_at: 2014-11-05 04:06:56 UTC  
> Url: https://github.com/boostorg/compute/pull/290#issuecomment-61757226  

@shehzan10 PR #293 should fix the `example.black_scholes` test. I'll look more into the `test_event` failures on NVIDIA. I'm thinking now this is more an NVIDIA-specific quirk rather than an OpenCL version issue.

---

## Comment 7

> Username: kylelutz  
> Created_at: 2014-11-05 04:10:12 UTC  
> Updated_at: 2014-11-05 04:10:26 UTC  
> Url: https://github.com/boostorg/compute/pull/290#issuecomment-61757425  

@shehzan10 and PR #294 should fix `test_kernel`.

---
