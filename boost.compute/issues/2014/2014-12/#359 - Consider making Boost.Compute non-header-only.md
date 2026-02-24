# #359 - Consider making Boost.Compute non-header-only [Open]

> Username: kylelutz  
> Created at: 2014-12-29 18:01:16 UTC  
> Updated at: 2017-03-21 04:14:53 UTC  
> Url: https://github.com/boostorg/compute/issues/359  

Consider making Boost.Compute non-header-only. This issue was raised a few times during the Boost formal review for Boost.Compute. There are a number of potential advantages including allowing the library to be thread-safe by default, reducing the amount of code in header files to improve compile times, and allowing the build system to convert `.cl` source files to strings in `.cpp` source files.  
  
This would be a large and non-backwards-compatible change and should be discussed with the community. Downsides include making the library slightly more difficult to use as it would now require separate compilation and installation steps before use.  
##

---

## Comment 1

> Username: ghost  
> Created at: 2014-12-29 18:02:51 UTC  
> Url: https://github.com/boostorg/compute/issues/359#issuecomment-68280998  

would you prefer discussion here or on the mailing list?

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-12-29 18:05:58 UTC  
> Url: https://github.com/boostorg/compute/issues/359#issuecomment-68281286  

The mailing list is probably better for large design discussions like this.

---

## Comment 3

> Username: ghost  
> Created at: 2014-12-29 18:07:33 UTC  
> Updated at: 2014-12-29 18:10:55 UTC  
> Url: https://github.com/boostorg/compute/issues/359#issuecomment-68281425  

alright, link to the mailing list is here https://groups.google.com/forum/#!forum/boost-compute in case anyone reads this and isn't aware.  
  
edit: https://groups.google.com/forum/#!topic/boost-compute/TpzeaHbZfuY

---

## Comment 4

> Username: ghost  
> Created at: 2015-01-03 03:13:12 UTC  
> Url: https://github.com/boostorg/compute/issues/359#issuecomment-68581181  

If you decide to make the library compilable, go only with CMake!
