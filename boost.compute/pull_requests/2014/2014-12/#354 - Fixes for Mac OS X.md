# #354 Fixes for Mac OS X [Merged]

> Username: kylelutz  
> Created at: 2014-12-23 06:42:18 UTC  
> Updated at: 2014-12-24 19:39:57 UTC  
> Merged at: 2014-12-24 19:39:56 UTC  
> Closed at: 2014-12-24 19:39:56 UTC  
> Url: https://github.com/boostorg/compute/pull/354  



---

## Comment 1

> Username: coveralls  
> Created_at: 2014-12-23 06:58:04 UTC  
> Url: https://github.com/boostorg/compute/pull/354#issuecomment-67926490  

[![Coverage Status](https://coveralls.io/builds/1650789/badge)](https://coveralls.io/builds/1650789)  
  
Coverage increased (+0.01%) when pulling **4d6b9d2a8e9aada1bcead158016d96bc78dc74a4 on osx-fixes** into **88811e345b9c78bee973321c87cef819ee1cff5c on develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-12-23 16:50:17 UTC  
> Url: https://github.com/boostorg/compute/pull/354#issuecomment-67972210  

@shehzan10 this updates some of the changes you made in PR #306, could you test this out and let me know if it still works for you?

---

## Comment 3

> Username: shehzan10  
> Created_at: 2014-12-23 17:32:21 UTC  
> Url: https://github.com/boostorg/compute/pull/354#issuecomment-67976953  

NVIDIA devices still fail all the tests in test_event with errors like the one below:  
  
```  
Device: Quadro K5000  
unknown location(0): fatal error in "lambda_callback": memory access violation at address: 0x00000000: no mapping at fault address  
/workspace/compute/test/test_event.cpp(28): last checkpoint  
  
*** 1 failure detected in test suite "TestEvent"  
```  
  
Intel Core i7 either gets stuck on event_to_std_future or throws the following error  
  
```  
build((detached from 4d6b9d2))$ BOOST_COMPUTE_DEFAULT_DEVICE=CPU ./test/test_event  
Running 3 test cases...  
Device: Intel(R) Core(TM) i7-4770K CPU @ 3.50GHz  
Device: Intel(R) Core(TM) i7-4770K CPU @ 3.50GHz  
Device: Intel(R) Core(TM) i7-4770K CPU @ 3.50GHz  
unknown location(0): fatal error in "event_to_std_future": std::runtime_error: Unknown error -1  
/workspace/compute/test/test_event.cpp(90): last checkpoint  
  
*** 1 failure detected in test suite "TestEvent"  
```  
  
Ps. I add the following lines to the tests to ensure the correct device and that's why you see the output.  
  
```  
boost::compute::device device = boost::compute::system::default_device();  
std::cout << "Device: " << device.name() << std::endl;  
```

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-12-23 18:40:36 UTC  
> Url: https://github.com/boostorg/compute/pull/354#issuecomment-67984023  

Well that's annoying, I'll revert it back to the version `1.2` check. Have you guys had any luck using event callbacks on NVIDIA hardware?

---

## Comment 5

> Username: coveralls  
> Created_at: 2014-12-24 03:56:04 UTC  
> Url: https://github.com/boostorg/compute/pull/354#issuecomment-68024007  

[![Coverage Status](https://coveralls.io/builds/1655250/badge)](https://coveralls.io/builds/1655250)  
  
Coverage increased (+0.01%) when pulling **0dbf745565aeecc1bd01c9ac6f64697fd7e37eb1 on osx-fixes** into **88811e345b9c78bee973321c87cef819ee1cff5c on develop**.

---

## Comment 6

> Username: coveralls  
> Created_at: 2014-12-24 04:20:42 UTC  
> Url: https://github.com/boostorg/compute/pull/354#issuecomment-68024906  

[![Coverage Status](https://coveralls.io/builds/1655289/badge)](https://coveralls.io/builds/1655289)  
  
Coverage increased (+0.01%) when pulling **2974265befeacf9353c9e0b4c7628b0598427b7e on osx-fixes** into **88811e345b9c78bee973321c87cef819ee1cff5c on develop**.

---

## Comment 7

> Username: roshanrags  
> Created_at: 2014-12-24 16:29:57 UTC  
> Url: https://github.com/boostorg/compute/pull/354#issuecomment-68062200  

Fixed everything on Intel HD4000 :)  
However my GeForce 650M is stuck in `core.function`  
![screen shot 2014-12-24 at 9 50 48 pm](https://cloud.githubusercontent.com/assets/4298494/5549796/a7a188d2-8bb7-11e4-8b22-900fc0454a80.png)

---

## Comment 8

> Username: coveralls  
> Created_at: 2014-12-24 16:44:09 UTC  
> Url: https://github.com/boostorg/compute/pull/354#issuecomment-68063047  

[![Coverage Status](https://coveralls.io/builds/1656854/badge)](https://coveralls.io/builds/1656854)  
  
Coverage increased (+0.01%) when pulling **bd89d498fe6bf29ac4015976f794740977d2b558 on osx-fixes** into **88811e345b9c78bee973321c87cef819ee1cff5c on develop**.

---

## Comment 9

> Username: kylelutz  
> Created_at: 2014-12-24 17:33:58 UTC  
> Updated_at: 2014-12-24 19:39:48 UTC  
> Url: https://github.com/boostorg/compute/pull/354#issuecomment-68065857  

Hmm, yeah it looks like there is a bug with NVIDIA GPUs on Apple when assigning structs in local memory (which is done in `serial_insertion_sort()` for that test). I've disabled those tests for now with NVIDIA on Apple, hopefully we can find a better fix. All the tests should pass now on Apple with the Intel GPUs and NVIDIA GPUs. (Still working on Intel CPUs on Apple, but that is a larger fix and will probably come in a later pull-request).

---

## Comment 10

> Username: coveralls  
> Created_at: 2014-12-24 17:46:11 UTC  
> Url: https://github.com/boostorg/compute/pull/354#issuecomment-68066428  

[![Coverage Status](https://coveralls.io/builds/1657005/badge)](https://coveralls.io/builds/1657005)  
  
Coverage decreased (-0.0%) when pulling **417cb03670adf4fa52dde2f0a6f0e4b89bf252ac on osx-fixes** into **88811e345b9c78bee973321c87cef819ee1cff5c on develop**.

---
