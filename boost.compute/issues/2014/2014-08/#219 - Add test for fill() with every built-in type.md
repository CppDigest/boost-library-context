# #219 - Add test for fill() with every built-in type [Closed]

> Username: kylelutz  
> Created at: 2014-08-08 04:51:24 UTC  
> Updated at: 2015-06-23 03:00:10 UTC  
> Closed at: 2015-06-23 03:00:05 UTC  
> Url: https://github.com/boostorg/compute/issues/219  

Add a test for the `fill()` algorithm with every built-in type (e.g. `int_`, `float_`, `uint4_`). This will provide good code coverage for the `fill()` algorithm and for the built-in type wrappers. Use the [`BOOST_AUTO_TEST_CASE_TEMPLATE`](http://www.boost.org/doc/libs/1_56_0/libs/test/doc/html/utf/user-guide/test-organization/auto-test-case-template.html) macro to repeat the same test code with every type.

---

## Comment 1

> Username: shehzan10  
> Created at: 2014-11-01 16:58:08 UTC  
> Url: https://github.com/boostorg/compute/issues/219#issuecomment-61374775  

I have started testing compute on our internal build machines. I have been able to run the tests on Tahiti and I notice this failure on our AMD 7970 (Tahiti):  
  
```  
Running 8 test cases...  
/home/shehzan/workspace/compute/include/boost/compute/command_queue.hpp(594): fatal error in "boost::compute::event boost::compute::command_queue::enqueue_fill_buffer(const boost::compute::buffer&, const void*, size_t, size_t, size_t, const boost::compute::wait_list&)": std::exception: Invalid Value  
/home/shehzan/workspace/compute/test/test_fill.cpp(173): last checkpoint  
  
*** 1 failure detected in test suite "TestFill"  
```  
  
Let me know if I can provide any further information.  
  
Is it possible to select which device the tests/examples run on. Such as the CPU?

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-11-01 17:15:16 UTC  
> Url: https://github.com/boostorg/compute/issues/219#issuecomment-61375407  

Hmm, that's strange.  
  
Can you try adding the following line before the call to `clEnqueueFillBuffer()` in `command_queue::enqueue_fill_buffer()` and let me know what it prints out:  
  
```  
std::cout << "fill: " << pattern_size << ", " << offset << ", " << size << std::endl;  
```  
  
As for changing the default device used, you can set the `BOOST_COMPUTE_DEFAULT_DEVICE_TYPE` environment variable to use the CPU instead of the GPU:  
  
```  
export BOOST_COMPUTE_DEFAULT_DEVICE_TYPE=CPU  
```  
  
See the documentation for [`boost::compute::system::default_device()`](http://kylelutz.github.io/compute/boost/compute/system.html) for more information.

---

## Comment 3

> Username: shehzan10  
> Created at: 2014-11-01 17:36:52 UTC  
> Updated at: 2014-11-01 17:41:01 UTC  
> Url: https://github.com/boostorg/compute/issues/219#issuecomment-61376078  

I added the cout line to /include/boost/compute/command_queue.hpp line 578.  
Here the output:  
  
```  
shehzan@patton:~/workspace/compute/build$ ./test/test_fill  
Running 8 test cases...  
fill: 4, 0, 4000  
fill: 4, 0, 4000  
fill: 4, 2000, 2000  
fill: 8, 0, 80  
fill: 8, 0, 80  
fill: 4, 0, 16  
fill: 4, 0, 12  
fill: 4, 4, 8  
fill: 4, 0, 16  
fill: 4, 0, 20  
fill: 4, 0, 16  
fill: 4, 0, 16  
fill: 4, 0, 16  
fill: 4, 0, 16  
fill: 4, 0, 0  
/home/shehzan/workspace/compute/include/boost/compute/command_queue.hpp(595): fatal error in "boost::compute::event boost::compute::command_queue::enqueue_fill_buffer(const boost::compute::buffer&, const void*, size_t, size_t, size_t, const boost::compute::wait_list&)": std::exception: Invalid Value  
/home/shehzan/workspace/compute/test/test_fill.cpp(173): last checkpoint  
  
*** 1 failure detected in test suite "TestFill"  
```  
  
Edit:  
I ran it with the BOOST_COMPUTE_DEFAULT_DEVICE=CPU, and the test_fill fails with the Intel Xeon too.

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-11-01 17:43:19 UTC  
> Url: https://github.com/boostorg/compute/issues/219#issuecomment-61376300  

Ahh, somewhere we're calling `fill()` with an empty range (`size == 0`) which I guess is technically invalid. I'll add a check for that.

---

## Comment 5

> Username: kylelutz  
> Created at: 2014-11-01 17:47:33 UTC  
> Url: https://github.com/boostorg/compute/issues/219#issuecomment-61376445  

Fixed in PR #289.

---

## Comment 6

> Username: shehzan10  
> Created at: 2014-11-01 17:49:08 UTC  
> Url: https://github.com/boostorg/compute/issues/219#issuecomment-61376514  

Sounds good.  
I ran it on an alternate machine with Intel 4770k and a Quadro K5000. All tests pass on the Intel CPU. Most fail on the Quadro K5000. I tried building it with CUDA enabled in CMake, but it doesn't make a difference. Let me know if you want me to create an issue for that.  
  
Here is a sample failure on the Quadro. I think all failures are this error.  
  
```  
build(master)$ ./test/test_buffer  
Running 9 test cases...  
unknown location(0): fatal error in "size": memory access violation at address: 0x00000000: no mapping at fault address  
  
*** 1 failure detected in test suite "TestBuffer"  
[Exit 201 ]  
```

---

## Comment 7

> Username: kylelutz  
> Created at: 2014-11-01 17:54:31 UTC  
> Url: https://github.com/boostorg/compute/issues/219#issuecomment-61376751  

Interesting, can you make a separate issue for that? The `size()` test in `test_buffer` is pretty simple, can you verify that other OpenCL functions work correctly on that platform/device?

---

## Comment 8

> Username: shehzan10  
> Created at: 2014-11-01 18:04:13 UTC  
> Url: https://github.com/boostorg/compute/issues/219#issuecomment-61377142  

It's not only test_buffer. 136/148 tests (including examples) fail. Examples fail with segfaults.  
I know OpenCL works on the device because we run our own tests on it. I also tested it with a Tesla K40 and even that fails similarly.

---

## Comment 9

> Username: kylelutz  
> Created at: 2014-11-01 18:08:07 UTC  
> Updated at: 2014-11-01 18:18:45 UTC  
> Url: https://github.com/boostorg/compute/issues/219#issuecomment-61377280  

Yeah, but if simply creating an OpenCL buffer fails, almost every other test is going to fail :-).  
  
Can you let me know what version of the OpenCL headers you're compiling against and what version of the NVIDIA OpenCL library you have? Also, are you using the OpenCL-ICD loader or linking directly against NVIDIA's libOpenCL.so?

---

## Comment 10

> Username: shehzan10  
> Created at: 2014-11-01 21:46:23 UTC  
> Url: https://github.com/boostorg/compute/issues/219#issuecomment-61384924  

Good suggestion. Using the AMD APP SDK's include file solved the problem. Thanks!

---

## Comment 11

> Username: jszuppe  
> Created at: 2015-06-22 11:50:39 UTC  
> Url: https://github.com/boostorg/compute/issues/219#issuecomment-114076780  

It can be closed, see https://github.com/boostorg/compute/issues/461.

---

## Comment 12

> Username: kylelutz  
> Created at: 2015-06-23 03:00:05 UTC  
> Url: https://github.com/boostorg/compute/issues/219#issuecomment-114336124  

Thanks!
