# #157 Fix point<T> divide and multiply to not to hardcode result as point<double> [Merged]

> Username: mloskot  
> Created at: 2018-10-21 18:36:11 UTC  
> Updated at: 2018-10-24 13:10:25 UTC  
> Merged at: 2018-10-24 13:08:40 UTC  
> Closed at: 2018-10-24 13:08:40 UTC  
> Url: https://github.com/boostorg/gil/pull/157  

The free variants of `operator/` and `operator*` now yield new type of `point<T>` with `T` promoted to type common for both operands.  
  
Add missing member `operator*=`, for symmetry.  
Add template function `iround(point<T>)` as no-op sink for `point` specialisations for `T` of integer types.  
Add explicit casts of arithmetic intermediate results to avoid compilation warnings.  
Replace redundant `point<T>` construction in return statement with uniform initialization.  
Add `static_assert` validating range of `axis_value` integer template parameter.  
  
Update `test/point/point.cpp` with basic checks of all `point<T>` members.  
  
### References  
  
Fixes #153  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-10-21 19:24:19 UTC  
> Updated_at: 2018-10-21 19:25:52 UTC  
> Url: https://github.com/boostorg/gil/pull/157#issuecomment-431696504  

This PR is causing failures of the old friend `test/image.cpp` checksum checks, on both  
  
- AppVeyor https://ci.appveyor.com/project/stefanseefeld/gil/builds/19676513/job/xadppbxh7v8a54q2  
  
```  
testing.capture-output bin.v2\libs\gil\test\image.test\msvc-14.1\debug\address-model-64\asynch-exceptions-on\threading-multi\image.run  
====== BEGIN OUTPUT ======  
Reading checksums from libs\gil\test\gil_reference_checksums.txt  
Checking checksum for bgr8_basic_red_x (crc=7f6e24e7)  
Checking checksum for bgr8_basic_white_x (crc=e4aaa1d3)  
Checking checksum for bgr8_views_original (crc=423f1dde)  
Checksum error in bgr8_views_cropped (crc=760a7319 != ff56b05e)  
Unknown exception  
   
EXIT STATUS: 1   
====== END OUTPUT ======  
```  
  
- Travis CI - https://travis-ci.org/mloskot/gil/builds/444434819  
  
```  
testing.capture-output bin.v2/libs/gil/test/image.test/gcc-7.3.0/debug/visibility-hidden/image.run  
====== BEGIN OUTPUT ======  
Reading checksums from libs/gil/test/gil_reference_checksums.txt  
Checking checksum for bgr8_basic_red_x (crc=7f6e24e7)  
Checking checksum for bgr8_basic_white_x (crc=e4aaa1d3)  
Checking checksum for bgr8_views_original (crc=423f1dde)  
Checksum error in bgr8_views_cropped (crc=760a7319 != ff56b05e)  
std::exception  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
On AppVeyor, we are expecting some image checksum failures, eg. https://ci.appveyor.com/project/stefanseefeld/gil/builds/19660664, but these above are different.  
  
I'll have to investigate what's going on

---

## Review 2 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-10-21 19:30:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/157#pullrequestreview-166797711  

📁 include/boost/gil/point.hpp

```diff
  79 |+             x = static_cast<T>(x / d);
  80 |+             y = static_cast<T>(y / d);
  81 |+         }
```

> Username: stefanseefeld  
> Created_at: 2018-10-21 19:30:33 UTC  
> Updated_at: 2018-10-23 20:01:38 UTC  
> Url: https://github.com/boostorg/gil/pull/157#discussion_r226875082  

Can you elaborate on how these explicit casts differ from the previous implicit ones in "x /= t" ?

> Username: mloskot  
> Created_at: 2018-10-21 20:09:25 UTC  
> Updated_at: 2018-10-23 20:01:38 UTC  
> Url: https://github.com/boostorg/gil/pull/157#discussion_r226876236  

Both conversions are equivalent. The implicit just rubs the compiler, especially in strict warnings mode, namely:  
  
```  
warning C4244: '/=': conversion from 'double' to 'T', possible loss of data  
  
warning: conversion from ‘double’ to ‘int’ may change value [-Wfloat-conversion]  
             x /= d;  
             ~~^~~~  
```  
  
 The explicit states the intention, well, explicitly, thus helps to avoid the warnings.


---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2018-10-21 19:36:07 UTC  
> Url: https://github.com/boostorg/gil/pull/157#issuecomment-431697359  

Interesting ! As far as I can see, the only semantic change of this PR changes the `*` and `/` operators to accept additional operand types (and then adjust the respective return types appropriately).  
So it sounds like this change causes a different point type to be instantiated in some test (`point<float>`, presumably ?), which then triggers some other type conversion(s) leading to the computational (numeric ?) error we observe.   
So with a bit of luck this coincidence will help us understand the root cause of the checksum error, right ?

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-10-21 21:20:51 UTC  
> Updated_at: 2018-10-21 21:22:17 UTC  
> Url: https://github.com/boostorg/gil/pull/157#issuecomment-431705058  

The problem was in the unexpected rounding or truncating towards zero like in here:  
  
```  
int x = 69;  
int ups = x / 2; // 34  
```  
  
while the `test/image.cpp` uses of `point<T>` actually rely on the fact that point division yields `point<double>`, ie.:  
  
```  
check_view(subimage_view(img_view, iround(img_view.dimensions()/4), iround(img_view.dimensions()/2)),prefix+"cropped");  
```  
  
what effectively means:  
  
```  
point<int> p(69, 36);  
point<double> pa = p / 2; // x = 34.5  
iround(pa);               // x = 35  
```  
  
Originally I submitted this version of `operator/` which, due to truncation towards zero, was not equivalent to `point<double>`, thus changed the behaviour (so, good catch by the `test/image.cpp` :-)):  
  
```  
template <typename T, typename D>  
auto operator/(point<T> const& p, D d) -> point<typename std::common_type<T, D>::type>  
{  
    static_assert(std::is_arithmetic<D>::value, "denominator is not arithmetic type");  
    using result_type = typename std::common_type<T, D>::type;  
    if (d < 0 || 0 < d)  
    {  
        return point<result_type>{  
            static_cast<result_type>(p.x) / d,  
            static_cast<result_type>(p.y) / d};  
    }  
    else  
    {  
        return point<result_type>{0, 0};  
    }  
}  
```  
  
I've just fixed it this way to preserve the expected behaviour:  
  
```  
    if (d < 0 || 0 < d)  
    {  
        double const x = p.x / static_cast<double>(d);  
        double const y = p.y / static_cast<double>(d);  
        return point<result_type>{  
            static_cast<result_type>(iround(x)),  
            static_cast<result_type>(iround(y))};  
    }  
```  
  
I think, this experience helps to get closer to the question asked in #153 about why authors of the original `point<double> operator/` implemented it that way.  
  
I think uses like `iround(img_view.dimensions()/4)` indicate the old implementation was fragile. The `point<T>` is de-facto an integer-based type and it should fullfil the promised behaviour internally, instead of asking users to remember to take care of rounding. The same `point<double>` dimension, one user may truncate or floor, another may ceil, leading to different results.

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-10-22 11:47:47 UTC  
> Url: https://github.com/boostorg/gil/pull/157#issuecomment-431811721  

Now, I'll have to deal with the failure of Travis job `COMPILER=clang++-5.0 VARIANT=ubsan_undefined` - https://travis-ci.org/boostorg/gil/jobs/444470812  - in the `toolbox/test/test.cpp`.  
Things are getting interesting :)

---

## Comment 6

> Username: mloskot  
> Created_at: 2018-10-22 16:59:21 UTC  
> Updated_at: 2018-10-22 17:53:40 UTC  
> Url: https://github.com/boostorg/gil/pull/157#issuecomment-431897272  

AFAIU, the `ubsan_undefined` failure is not reporting any issues in GIL, but somewhere else!  
  
```  
====== BEGIN OUTPUT ======  
boost/function/function_template.hpp:768:14: runtime error: call to function boost::detail::function::void_function_invoker0<void (*)(), void>::invoke(boost::detail::function::function_buffer&) through pointer to incorrect function type 'void (*)(boost::detail::function::function_buffer &)'  
/home/travis/build/boostorg/boost-root/./boost/function/function_template.hpp:116: note: boost::detail::function::void_function_invoker0<void (*)(), void>::invoke(boost::detail::function::function_buffer&) defined here  
    #0 0x7f985638b92a in boost::function0<void>::operator()() const /home/travis/build/boostorg/boost-root/./boost/function/function_template.hpp:768:14  
    #1 0x7f985638b73a in boost::detail::forward::operator()() /home/travis/build/boostorg/boost-root/./boost/test/impl/execution_monitor.ipp:1312:32  
    #2 0x7f985638b73a in boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) /home/travis/build/boostorg/boost-root/./boost/function/function_template.hpp:138  
    #3 0x7f9856385b91 in boost::execution_monitor::catch_signals(boost::function<int ()> const&) /home/travis/build/boostorg/boost-root/./boost/test/impl/execution_monitor.ipp:875:16  
    #4 0x7f9856385df8 in boost::execution_monitor::execute(boost::function<int ()> const&) /home/travis/build/boostorg/boost-root/./boost/test/impl/execution_monitor.ipp:1214:16  
    #5 0x7f9856387c0e in boost::execution_monitor::vexecute(boost::function<void ()> const&) /home/travis/build/boostorg/boost-root/./boost/test/impl/execution_monitor.ipp:1321:5  
    #6 0x7f98563d2858 in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned int) /home/travis/build/boostorg/boost-root/./boost/test/impl/unit_test_monitor.ipp:49:9  
    #7 0x7f9856397f44 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) /home/travis/build/boostorg/boost-root/./boost/test/impl/framework.ipp:789:44  
    #8 0x7f98563980f0 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) /home/travis/build/boostorg/boost-root/./boost/test/impl/framework.ipp:737:54  
    #9 0x7f98563980f0 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) /home/travis/build/boostorg/boost-root/./boost/test/impl/framework.ipp:737:54  
    #10 0x7f9856391a41 in boost::unit_test::framework::run(unsigned long, bool) /home/travis/build/boostorg/boost-root/./boost/test/impl/framework.ipp:1631:29  
    #11 0x7f98563cf4eb in boost::unit_test::unit_test_main(bool (*)(), int, char**) /home/travis/build/boostorg/boost-root/./boost/test/impl/unit_test_main.ipp:247:9  
    #12 0x7f9854eaaf44 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21f44)  
    #13 0x40a4f5 in _start (/home/travis/build/boostorg/boost-root/bin.v2/libs/gil/toolbox/test/test.test/clang-linux-5.0.2/ubsan_undefined/visibility-hidden/test+0x40a4f5)  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
There is no GIL location at the top of the trace.  
  
Eventually, I've prepared mwe and reported it here https://github.com/boostorg/function/pull/29

---

## Comment 7

> Username: mloskot  
> Created_at: 2018-10-24 07:34:40 UTC  
> Url: https://github.com/boostorg/gil/pull/157#issuecomment-432545989  

@stefanseefeld The CI-s are green now. Approve?

---
