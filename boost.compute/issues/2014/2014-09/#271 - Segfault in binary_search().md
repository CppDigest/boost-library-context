# #271 - Segfault in binary_search() [Closed]

> Username: kylelutz  
> Created at: 2014-09-29 03:10:57 UTC  
> Updated at: 2016-11-01 20:42:51 UTC  
> Closed at: 2016-10-14 01:58:36 UTC  
> Url: https://github.com/boostorg/compute/issues/271  

I was looking at the coverage report for the `detail::binary_find()` algorithm (https://coveralls.io/files/301660135) at tried writing a test which used the `binary_find_kernel` (by making the input range larger than 128 values) but got a segfault when running:  
  
```  
Running 3 test cases...  
Segmentation fault (core dumped)  
```  
  
Here's the test case:  
  
```  
BOOST_AUTO_TEST_CASE(binary_search_large)  
{  
    compute::vector<int> vec(1000, context);  
    compute::iota(vec.begin(), vec.end(), 1, queue);  
  
    BOOST_CHECK(compute::binary_search(vec.begin(), vec.end(), int(0)) == false);  
    BOOST_CHECK(compute::binary_search(vec.begin(), vec.end(), int(1)) == true);  
    BOOST_CHECK(compute::binary_search(vec.begin(), vec.end(), int(1000)) == true);  
    BOOST_CHECK(compute::binary_search(vec.begin(), vec.end(), int(1001)) == false);  
}  
```  
  
@roshanr95 can you reproduce this? Any ideas?  
##

---

## Comment 1

> Username: roshanrags  
> Created at: 2014-10-16 08:39:50 UTC  
> Updated at: 2014-10-16 08:49:25 UTC  
> Url: https://github.com/boostorg/compute/issues/271#issuecomment-59330731  

Funny.. It works fine on both my HD4000 as well as GT650M but fails on Travis(https://travis-ci.org/roshanr95/compute/jobs/38130958). Probably some problem in AMD devices?  
  
I had tested the kernel on my setup with 2 and 4 threads but don't remember if I ever pushed any to Github. I'll take a look at my Travis logs.  
  
**_Update**_: Couldn't find any log without 128 threads...

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-10-23 02:51:44 UTC  
> Url: https://github.com/boostorg/compute/issues/271#issuecomment-60186167  

Interesting. Yeah, I can only get this to fail on my system with the AMD OpenCL library (with the CPU). Have you tried it with that?  
  
Also, it would be good to determine if this is actually just an AMD bug and, if so, find a small reproducible test case. Is there any output you could add to help narrow this down? Let me know if you have any ideas.

---

## Comment 3

> Username: roshanrags  
> Created at: 2014-10-23 06:28:50 UTC  
> Url: https://github.com/boostorg/compute/issues/271#issuecomment-60197712  

Travis is my go-to for AMD devices. I'll run a few more builds on it...

---

## Comment 4

> Username: jszuppe  
> Created at: 2016-10-13 10:06:13 UTC  
> Url: https://github.com/boostorg/compute/issues/271#issuecomment-253471393  

Currently in [test_binary_search.cpp](https://github.com/boostorg/compute/blob/master/test/test_binary_search.cpp) there is a test with a large input and the problem is gone for some time (there were some changes introduced to [binary_find.hpp](https://github.com/boostorg/compute/blob/master/include/boost/compute/algorithm/detail/binary_find.hpp) and maybe they fixed this problem). IMHO, this can be closed.
