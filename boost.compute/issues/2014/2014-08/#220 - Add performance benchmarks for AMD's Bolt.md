# #220 - Add performance benchmarks for AMD's Bolt [Closed]

> Username: kylelutz  
> Created at: 2014-08-08 04:54:06 UTC  
> Updated at: 2015-05-08 03:08:52 UTC  
> Closed at: 2015-05-08 03:08:48 UTC  
> Url: https://github.com/boostorg/compute/issues/220  

Add performance benchmarks for AMD's [Bolt](https://github.com/HSA-Libraries/Bolt) library. Similar to the `perf_thrust_*` benchmarks which are used for comparing the performance of Boost.Compute against NVIDIA's Thrust library.

---

## Comment 1

> Username: jszuppe  
> Created at: 2015-04-30 14:38:05 UTC  
> Url: https://github.com/boostorg/compute/issues/220#issuecomment-97822784  

Commit https://github.com/haahh/compute/commit/309a201a61efdb6fca86131ae896dbb5d6a68745 adds first Bolt perf test. I will add all possible tests and then make PR.  
  
Bolt requires C++11, so performance tests for Bolt are build only if BOOST_COMPUTE_USE_CPP11 option is enabled. if BOOST_COMPUTE_HAVE_BOLT option is on and BOOST_COMPUTE_USE_CPP11 is off, warning message is printed. I couldn't find any better way to communicate this requirement, is there?  
  
Bolt requires certain Boost libraries: thread system date_time chrono.

---

## Comment 2

> Username: jszuppe  
> Created at: 2015-05-07 08:00:52 UTC  
> Url: https://github.com/boostorg/compute/issues/220#issuecomment-99761067  

Can be closed, see https://github.com/boostorg/compute/pull/453.

---

## Comment 3

> Username: kylelutz  
> Created at: 2015-05-08 03:08:48 UTC  
> Url: https://github.com/boostorg/compute/issues/220#issuecomment-100084384  

Awesome! Thanks!
