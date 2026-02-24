# #20 - What compilers, operating systems, and video cards(devices) are supported? [Closed]

> Username: AlexeyAB  
> Created at: 2013-08-21 09:08:29 UTC  
> Updated at: 2013-09-07 21:24:22 UTC  
> Closed at: 2013-09-07 21:24:22 UTC  
> Url: https://github.com/boostorg/compute/issues/20  

Greetings.  
What compilers, operating systems, and video cards(devices) are supported Boost.Compute?  
  
It would be nice if the main page was something like:  
Supported:  
- Compilers: GCC >= 4.7.2, MSVC >= 11, Clang >= 3.3  
- OS: Win, Linux, Unix, XNU  
- Devices: GeForce / Tesla (GF1xx, GK1xx), Radeon XXX, Xeon Phi, All MultiCores x86_64 CPUs  
  
And which ones supposedly are supported, and tested on which of these?  
  
Regards, Alexey

---

## Comment 1

> Username: kylelutz  
> Created at: 2013-08-22 01:32:46 UTC  
> Url: https://github.com/boostorg/compute/issues/20#issuecomment-23063052  

Hi Alexey,  
  
Thanks for raising the issue. I've added a page to the documention listing the supported compilers, platforms, and OpenCL implementations: http://kylelutz.github.io/compute/boost_compute/platforms_and_compilers.html  
  
-kyle

---

## Comment 2

> Username: ddemidov  
> Created at: 2013-08-22 02:36:27 UTC  
> Url: https://github.com/boostorg/compute/issues/20#issuecomment-23064925  

Hi Kyle,   
  
I see that you've added pocl to the list of supported platforms. The latest pocl version fails the following tests on my machine:  
  
```  
The following tests FAILED:  
          5 - core.image2d (Failed)  
          7 - core.image_sampler (Failed)  
         20 - algorithm.copy_if (Failed)  
         38 - algorithm.partial_sum (Failed)  
         39 - algorithm.partition (Failed)  
         41 - algorithm.random_fill (Not Run)  
         44 - algorithm.remove (Failed)  
         47 - algorithm.scan (Failed)  
         61 - container.vector (Failed)  
Errors while running CTest  
make: *** [test] Error 8  
```

---

## Comment 3

> Username: AlexeyAB  
> Created at: 2013-08-22 12:47:33 UTC  
> Url: https://github.com/boostorg/compute/issues/20#issuecomment-23087259  

Hi Kyle,  
  
Big thanks!  
Can you say, whether Boost.Compute already is using somewhere in real production, and when planned to include Boost.Compute to official Boost library?  
  
Regards, Alexey

---

## Comment 4

> Username: kylelutz  
> Created at: 2013-08-29 03:42:56 UTC  
> Url: https://github.com/boostorg/compute/issues/20#issuecomment-23465121  

Sorry for the delay guys, I've been very busy.  
  
@ddemidov I have a few changes which improve the POCL support (and skip tests which use OpenCL features that are not yet supported). I'll try to get them pushed sometime this week. After that the tests should pass and I'll keep POCL as one of the main supported OpenCL implementations.  
  
@AlexeyAB I have heard from a handful of people who are using the library in their applications. As for becoming an official Boost library, there is still a bit of work to be done before it can be submitted through the Boost review process (and I'm always looking for people to help out if you're interested).

---

## Comment 5

> Username: kylelutz  
> Created at: 2013-09-07 19:16:04 UTC  
> Url: https://github.com/boostorg/compute/issues/20#issuecomment-24007775  

@ddemidov I've pushed the changes which fix the test failures on POCL. Now all of the Boost.Compute tests pass when using POCL. For reference I compiled POCL with `./configure --enable-direct-linkage --disable-icd && make` and then ran the Boost.Compute tests with `POCL_BUILDING=1`. Can you test it out on your machines?

---

## Comment 6

> Username: kylelutz  
> Created at: 2013-09-07 19:17:35 UTC  
> Url: https://github.com/boostorg/compute/issues/20#issuecomment-24007804  

@AlexeyAB Can you let me know if the supported platform/compiler information is what you were looking for. If so can we close the issue? Otherwise let me know what other information you would like to see.  
  
Thanks,  
Kyle

---

## Comment 7

> Username: ddemidov  
> Created at: 2013-09-07 19:40:51 UTC  
> Url: https://github.com/boostorg/compute/issues/20#issuecomment-24008195  

Hi Kyle,  
  
All tests do pass now:  
  
```  
100% tests passed, 0 tests failed out of 80  
  
Total Test time (real) = 974.65 sec  
```  
  
Judging by the execution time, POCL is another platform where kernel caching is desperately needed :).

---

## Comment 8

> Username: kylelutz  
> Created at: 2013-09-07 19:44:01 UTC  
> Url: https://github.com/boostorg/compute/issues/20#issuecomment-24008262  

Excellent!  
  
And yeah, kernel caching is right near the top of my TODO list. And actually I should have some preliminary caching work going in later today.

---

## Comment 9

> Username: AlexeyAB  
> Created at: 2013-09-07 21:21:08 UTC  
> Url: https://github.com/boostorg/compute/issues/20#issuecomment-24009943  

Hi Kyle,  
  
Thanks. Yes, I found everything I was looking for, the question can be closed.  
  
Best regards, Alexey

---

## Comment 10

> Username: kylelutz  
> Created at: 2013-09-07 21:24:22 UTC  
> Url: https://github.com/boostorg/compute/issues/20#issuecomment-24010014  

Cool! Thanks!
