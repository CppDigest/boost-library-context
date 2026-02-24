# #485 - algorithm_channel_arithmetic test seems random [Open]

> Username: trex58  
> Created at: 2020-04-20 16:35:48 UTC  
> Updated at: 2020-05-11 12:44:35 UTC  
> Url: https://github.com/boostorg/gil/issues/485  

### Actual behavior  
  
Porting Boost 1.69 to AIX, I see the test **algorithm_channel_arithmetic** failing on AIX randomly (depending on the -O? option I use).  
Then, looking at Fed/Intel, though the test seems to succeed, I see that intermediate values are random, probably being the root cause of my issue on AIX.  
  
With same executable (built with addition of one line of printf in file below) on Fed/Intel:  
   ../bin.v2/libs/gil/test/channel/algorithm_channel_arithmetic.test/gcc-9.2.1/debug/visibility-hidden/**algorithm_channel_arithmetic** , and launching it several times, I have the following different results:  
```  
1)  
TONY: BEG f.min_v_: 0l f.max_v_: 255l  
TONY: BEG f.min_v_: 4294967168l f.max_v_: 127l  
TONY: BEG f.min_v_: 0l f.max_v_: 65535l  
TONY: BEG f.min_v_: 4294934528l f.max_v_: 32767l  
TONY: BEG f.min_v_: 0l f.max_v_: 4294967295l  
TONY: BEG f.min_v_: 2147483648l f.max_v_: 2147483647l  
TONY: BEG f.min_v_: 168336160l f.max_v_: 168336192l     <===  
TONY: BEG f.min_v_: 168336176l f.max_v_: 168336208l  
TONY: BEG f.min_v_: 0l f.max_v_: 255l  
2)  
TONY: BEG f.min_v_: 0l f.max_v_: 255l  
TONY: BEG f.min_v_: 4294967168l f.max_v_: 127l  
TONY: BEG f.min_v_: 0l f.max_v_: 65535l  
TONY: BEG f.min_v_: 4294934528l f.max_v_: 32767l  
TONY: BEG f.min_v_: 0l f.max_v_: 4294967295l  
TONY: BEG f.min_v_: 2147483648l f.max_v_: 2147483647l  
TONY: BEG f.min_v_: 4049837264l f.max_v_: 4049837296l     <===  
TONY: BEG f.min_v_: 4049837280l f.max_v_: 4049837312l  
TONY: BEG f.min_v_: 0l f.max_v_: 255l  
3)  
TONY: BEG f.min_v_: 0l f.max_v_: 255l  
TONY: BEG f.min_v_: 4294967168l f.max_v_: 127l  
TONY: BEG f.min_v_: 0l f.max_v_: 65535l  
TONY: BEG f.min_v_: 4294934528l f.max_v_: 32767l  
TONY: BEG f.min_v_: 0l f.max_v_: 4294967295l  
TONY: BEG f.min_v_: 2147483648l f.max_v_: 2147483647l  
TONY: BEG f.min_v_: 3091145712l f.max_v_: 3091145744l     <===  
TONY: BEG f.min_v_: 3091145728l f.max_v_: 3091145760l  
TONY: BEG f.min_v_: 0l f.max_v_: 255l  
4)  
TONY: BEG f.min_v_: 0l f.max_v_: 255l  
TONY: BEG f.min_v_: 4294967168l f.max_v_: 127l  
TONY: BEG f.min_v_: 0l f.max_v_: 65535l  
TONY: BEG f.min_v_: 4294934528l f.max_v_: 32767l  
TONY: BEG f.min_v_: 0l f.max_v_: 4294967295l  
TONY: BEG f.min_v_: 2147483648l f.max_v_: 2147483647l  
TONY: BEG f.min_v_: 2127298960l f.max_v_: 2127298992l     <===  
TONY: BEG f.min_v_: 2127298976l f.max_v_: 2127299008l  
TONY: BEG f.min_v_: 0l f.max_v_: 255l  
```  
  
However, on AIX, when it breaks with errors, I ALWAYS have:  
```  
TONY: BEGINNING f.min_v_: 0l f.max_v_: 255l  
TONY: BEGINNING f.min_v_: 4294967168l f.max_v_: 127l  
TONY: BEGINNING f.min_v_: 0l f.max_v_: 65535l  
TONY: BEGINNING f.min_v_: 4294934528l f.max_v_: 32767l  
TONY: BEGINNING f.min_v_: 0l f.max_v_: 4294967295l  
TONY: BEGINNING f.min_v_: 2147483648l f.max_v_: 2147483647l  
TONY: BEGINNING f.min_v_: 4294954660l f.max_v_: 4294954656l     <===  
TONY: BEGINNING f.min_v_: 4294954536l f.max_v_: 4294954528l  
TONY: BEGINNING f.min_v_: 0l f.max_v_: 255l  
```  
  
But, still on AIX, this generates errors:  
```  
+ ../bin.v2/libs/gil/test/channel/algorithm_channel_arithmetic.test/gcc-8.4.0/debug/address-model-64/visibility-hidden/algorithm_channel_arithmetic  
Running 30 test cases...  
../libs/gil/test/channel/algorithm_channel_arithmetic.cpp(54): error: in "packed_channel_reference<unsigned long>": check f.min_v_ > f.max_v_ has failed [ <= ]  
../libs/gil/test/channel/algorithm_channel_arithmetic.cpp(58): error: in "packed_dynamic_channel_reference<unsigned long>": check v2 == v3 has failed [ != ]  
../libs/gil/test/channel/algorithm_channel_arithmetic.cpp(58): error: in "packed_dynamic_channel_reference<unsigned long>": check v2 == v3 has failed [ != ]  
```  
  
### Expected  behavior  
  
I expect the intermediate values of f.min_v_ and f.max_v_ to be the same when I run the test.  
I'd like someone checking that this issue does appear in the official Boost environment.  
  
### C++ Minimal Working Example  
  
Let's add a printf line at the begining of the test_channel_arithmetic_mutable() routine of the file libs/gil/test/channel/algorithm_channel_arithmetic.cpp .  
  
```  
libs/gil/test/channel/algorithm_channel_arithmetic.cpp :  
template <typename ChannelFixtureBase>  
void test_channel_arithmetic_mutable(boost::mpl::true_)  
{  
    using fixture_t = fixture::channel<ChannelFixtureBase>;  
    using channel_value_t = typename fixture_t::channel_value_t;  
    fixture_t f;  
    channel_value_t const v = f.min_v_;  
printf("TONY: BEG f.min_v_: %ul f.max_v_: %ul \n", f.min_v_, f.max_v_ );  
   ....  
```  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version: GCC 9.3.1  20200408 (Red Hat 9.3.1-2)  
- Build settings:   
- Version (Git ref or `<boost/version.hpp>`): v1.69

---

## Comment 1

> Username: mloskot  
> Created at: 2020-04-20 16:59:17 UTC  
> Updated at: 2020-04-20 16:59:25 UTC  
> Url: https://github.com/boostorg/gil/issues/485#issuecomment-616683970  

Thanks for the report. Sorry, but I only skimmed it.  
  
> `printf("TONY: BEG f.min_v_: %ul f.max_v_: %ul \n", f.min_v_, f.max_v_ );`  
  
The channels can be signed too, but I believe you are aware of that.

---

## Comment 2

> Username: trex58  
> Created at: 2020-04-20 17:14:26 UTC  
> Updated at: 2020-04-20 17:18:24 UTC  
> Url: https://github.com/boostorg/gil/issues/485#issuecomment-616693197  

I do no master Boost.  
I used %ul because my error message on AIX is talking about "unsigned long".  
Anyway, using %16ld, I see:  
```  
1)  
TONY: BEG f.min_v_:                0 f.max_v_:              255  
TONY: BEG f.min_v_:       4294967168 f.max_v_:              127  
TONY: BEG f.min_v_:                0 f.max_v_:            65535  
TONY: BEG f.min_v_:       4294934528 f.max_v_:            32767  
TONY: BEG f.min_v_:                0 f.max_v_:       4294967295  
TONY: BEG f.min_v_:       2147483648 f.max_v_:       2147483647  
TONY: BEG f.min_v_:  140729274827696 f.max_v_:  140729274827728   <===  
TONY: BEG f.min_v_:  140729274827712 f.max_v_:  140729274827744  
TONY: BEG f.min_v_:                0 f.max_v_:              255  
2)  
TONY: BEG f.min_v_:                0 f.max_v_:              255  
TONY: BEG f.min_v_:       4294967168 f.max_v_:              127  
TONY: BEG f.min_v_:                0 f.max_v_:            65535  
TONY: BEG f.min_v_:       4294934528 f.max_v_:            32767  
TONY: BEG f.min_v_:                0 f.max_v_:       4294967295  
TONY: BEG f.min_v_:       2147483648 f.max_v_:       2147483647  
TONY: BEG f.min_v_:  140730840531376 f.max_v_:  140730840531408   <===  
TONY: BEG f.min_v_:  140730840531392 f.max_v_:  140730840531424  
TONY: BEG f.min_v_:                0 f.max_v_:              255  
3)  
TONY: BEG f.min_v_:                0 f.max_v_:              255  
TONY: BEG f.min_v_:       4294967168 f.max_v_:              127  
TONY: BEG f.min_v_:                0 f.max_v_:            65535  
TONY: BEG f.min_v_:       4294934528 f.max_v_:            32767  
TONY: BEG f.min_v_:                0 f.max_v_:       4294967295  
TONY: BEG f.min_v_:       2147483648 f.max_v_:       2147483647  
TONY: BEG f.min_v_:  140725078286400 f.max_v_:  140725078286432   <===  
TONY: BEG f.min_v_:  140725078286416 f.max_v_:  140725078286448  
TONY: BEG f.min_v_:                0 f.max_v_:              255  
```  
Moreover, I also see that the swap() routine does not swap. However, I do not master what this code is aimed to do ; just trying to guess.  
```  
TONY: BEG f.min_v_:       2147483648 f.max_v_:       2147483647  
TONY: BEF swap f.min_v_:       2147483648 f.max_v_:       2147483647  
TONY: AFT swap f.min_v_:       2147483647 f.max_v_:       2147483648     Swap OK.  
TONY: v2       2147483647 v3       2147483647  
TONY: BEG f.min_v_:  140725539387952 f.max_v_:  140725539387984  
TONY: BEF swap f.min_v_:  140725539387952 f.max_v_:  140725539387984  
TONY: AFT swap f.min_v_:  140725539387952 f.max_v_:  140725539387984     No swap done.  
TONY: v2  140725539387952 v3  140725539387984  
TONY: BEG f.min_v_:  140725539387968 f.max_v_:  140725539388000  
TONY: BEF swap f.min_v_:  140725539387968 f.max_v_:  140725539388000  
TONY: AFT swap f.min_v_:  140725539387968 f.max_v_:  140725539388000  
```  
  
Is that correct behavior ?  
```  
printf("TONY: BEF swap f.min_v_: %16ld f.max_v_: %16ld \n", f.min_v_, f.max_v_ );  
    BOOST_TEST(f.min_v_ <= f.max_v_);  
    std::swap(f.min_v_, f.max_v_);  
printf("TONY: AFT swap f.min_v_: %16ld f.max_v_: %16ld \n", f.min_v_, f.max_v_ );  
    BOOST_TEST(f.min_v_ > f.max_v_);  
```

---

## Comment 3

> Username: mloskot  
> Created at: 2020-04-20 17:22:20 UTC  
> Updated at: 2020-04-20 17:22:27 UTC  
> Url: https://github.com/boostorg/gil/issues/485#issuecomment-616697823  

> `TONY: BEG f.min_v_:       4294967168 f.max_v_:              127`  
  
For the `f.min_v_` above, which is 8-bit value of `-128`, something is casting it to unsigned 32-bit integer: `2^32 - 128`, what becomes 4294967168.  
  
I'm not sure what is happening, and I can't look into details now.

---

## Comment 4

> Username: trex58  
> Created at: 2020-04-20 18:01:11 UTC  
> Url: https://github.com/boostorg/gil/issues/485#issuecomment-616718851  

OK. I'm not in a hurry. Anyway, I think that there is an issue here. Maybe it has been fixed within last versions, I don't know, but I'd like to know.  
Please update this issue so that I know what I have to do for AIX.

---

## Comment 5

> Username: mloskot  
> Created at: 2020-04-22 09:10:32 UTC  
> Url: https://github.com/boostorg/gil/issues/485#issuecomment-617654842  

@trex58 Yes, it looks like a bug somewhere in packed and/or bit-aligned images, possibly big-endian related. I am fluent yet about internals of those types, so it is hard to tell from top of my head.  
  
It would be interesting to see the results for the 1.73 (soon to be released).  
  
This is not going to be forgotten issue, but it may take time until I look into it (I will likely need QEMU running big-endian Unix).

---

## Comment 6

> Username: trex58  
> Created at: 2020-05-11 12:44:35 UTC  
> Url: https://github.com/boostorg/gil/issues/485#issuecomment-626678851  

I'm now using v1.73.0. And I think I still see the same behavior.  
  
A) Random intermediate values.  
  
1rst launch of test  
```  
# ../bin.v2/libs/gil/test/core/channel/algorithm_channel_arithmetic.test/gcc-9/debug/threading-multi/visibility-hidden/algorithm_channel_arithmetic 2>&1 | grep BEG  
TONY: BEG f.min_v_:                0 f.max_v_:              255  
TONY: BEG f.min_v_:       4294967168 f.max_v_:              127  
TONY: BEG f.min_v_:                0 f.max_v_:            65535  
TONY: BEG f.min_v_:       4294934528 f.max_v_:            32767  
TONY: BEG f.min_v_:                0 f.max_v_:       4294967295  
TONY: BEG f.min_v_:       2147483648 f.max_v_:       2147483647  
TONY: BEG f.min_v_:  140725939138380 f.max_v_:  140725939138376  
TONY: BEG f.min_v_:  140725939138264 f.max_v_:  140725939138256  
TONY: BEG f.min_v_:                0 f.max_v_:              255  
TONY: BEG f.min_v_:       4294967168 f.max_v_:              127  
TONY: BEG f.min_v_:                0 f.max_v_:            65535  
TONY: BEG f.min_v_:       4294934528 f.max_v_:            32767  
TONY: BEG f.min_v_:                0 f.max_v_:       4294967295  
TONY: BEG f.min_v_:       2147483648 f.max_v_:       2147483647  
TONY: BEG f.min_v_:  140725939138348 f.max_v_:  140725939138344  
TONY: BEG f.min_v_:  140725939138232 f.max_v_:  140725939138224  
TONY: BEG f.min_v_:  140725939138336 f.max_v_:  140725939138328  
....  
```  
2nd launch of test.  
```  
TONY: BEG f.min_v_:                0 f.max_v_:              255  
TONY: BEG f.min_v_:       4294967168 f.max_v_:              127  
TONY: BEG f.min_v_:                0 f.max_v_:            65535  
TONY: BEG f.min_v_:       4294934528 f.max_v_:            32767  
TONY: BEG f.min_v_:                0 f.max_v_:       4294967295  
TONY: BEG f.min_v_:       2147483648 f.max_v_:       2147483647  
TONY: BEG f.min_v_:  140729678367580 f.max_v_:  140729678367576    <<<<<  
TONY: BEG f.min_v_:  140729678367464 f.max_v_:  140729678367456  
TONY: BEG f.min_v_:                0 f.max_v_:              255  
TONY: BEG f.min_v_:       4294967168 f.max_v_:              127  
TONY: BEG f.min_v_:                0 f.max_v_:            65535  
TONY: BEG f.min_v_:       4294934528 f.max_v_:            32767  
TONY: BEG f.min_v_:                0 f.max_v_:       4294967295  
TONY: BEG f.min_v_:       2147483648 f.max_v_:       2147483647  
TONY: BEG f.min_v_:  140729678367548 f.max_v_:  140729678367544  
TONY: BEG f.min_v_:  140729678367432 f.max_v_:  140729678367424  
TONY: BEG f.min_v_:  140729678367536 f.max_v_:  140729678367528  
....  
```  
3rd launch  
```  
TONY: BEG f.min_v_:                0 f.max_v_:              255  
TONY: BEG f.min_v_:       4294967168 f.max_v_:              127  
TONY: BEG f.min_v_:                0 f.max_v_:            65535  
TONY: BEG f.min_v_:       4294934528 f.max_v_:            32767  
TONY: BEG f.min_v_:                0 f.max_v_:       4294967295  
TONY: BEG f.min_v_:       2147483648 f.max_v_:       2147483647  
TONY: BEG f.min_v_:  140724722884844 f.max_v_:  140724722884840  
TONY: BEG f.min_v_:  140724722884728 f.max_v_:  140724722884720  
TONY: BEG f.min_v_:                0 f.max_v_:              255  
TONY: BEG f.min_v_:       4294967168 f.max_v_:              127  
TONY: BEG f.min_v_:                0 f.max_v_:            65535  
TONY: BEG f.min_v_:       4294934528 f.max_v_:            32767  
TONY: BEG f.min_v_:                0 f.max_v_:       4294967295  
TONY: BEG f.min_v_:       2147483648 f.max_v_:       2147483647  
TONY: BEG f.min_v_:  140724722884812 f.max_v_:  140724722884808  <<<<  
TONY: BEG f.min_v_:  140724722884696 f.max_v_:  140724722884688  
TONY: BEG f.min_v_:  140724722884800 f.max_v_:  140724722884792  
```  
  
B) swap no done  
```  
# ../bin.v2/libs/gil/test/core/channel/algorithm_channel_arithmetic.test/gcc-9/debug/threading-multi/visibility-hidden/algorithm_channel_arithmetic 2>&1 | grep -v BEG  
TONY: BEF swap f.min_v_:                0 f.max_v_:              255  
TONY: AFT swap f.min_v_:              255 f.max_v_:                0  
TONY: v2              255 v3              255  
TONY: BEF swap f.min_v_:       4294967168 f.max_v_:              127  
TONY: AFT swap f.min_v_:              127 f.max_v_:       4294967168  
TONY: v2              127 v3              127  
TONY: BEF swap f.min_v_:                0 f.max_v_:            65535  
TONY: AFT swap f.min_v_:            65535 f.max_v_:                0  
TONY: v2            65535 v3            65535  
TONY: BEF swap f.min_v_:       4294934528 f.max_v_:            32767  
TONY: AFT swap f.min_v_:            32767 f.max_v_:       4294934528  
TONY: v2            32767 v3            32767  
TONY: BEF swap f.min_v_:                0 f.max_v_:       4294967295  
TONY: AFT swap f.min_v_:       4294967295 f.max_v_:                0  
TONY: v2       4294967295 v3       4294967295  
TONY: BEF swap f.min_v_:       2147483648 f.max_v_:       2147483647  
TONY: AFT swap f.min_v_:       2147483647 f.max_v_:       2147483648    Swap done  
TONY: v2       2147483647 v3       2147483647  
TONY: BEF swap f.min_v_:  140725453353352 f.max_v_:  140725453353348  
TONY: AFT swap f.min_v_:  140725453353360 f.max_v_:  140725453353356   No swap done  
TONY: v2  140725453353372 v3  140725453353368  
TONY: BEF swap f.min_v_:  140725453353280 f.max_v_:  140725453353272  
TONY: AFT swap f.min_v_:  140725453353296 f.max_v_:  140725453353288  
```  
  
Please note that I see 59 warning messages saying things like:  
```  
../boost/gil/channel_algorithm.hpp:82:67: warning: conversion from ‘long unsigned int’ to ‘int’ may change value [-Wconversion]  
../libs/gil/test/core/channel/algorithm_channel_arithmetic.cpp:40:14: warning: conversion from ‘int’ to ‘boost::gil::test::fixture::channel_value<unsigned char>::channel_t’ {aka ‘unsigned char’} may change value [-Wconversion]  
...  
../boost/gil/channel.hpp:513:115: warning: conversion from ‘int’ to ‘boost::gil::detail::packed_channel_reference_base<boost::gil::packed_channel_reference<short unsigned int, 11, 5, true>, short unsigned int, 5, true>::bitfield_t’ {aka ‘short unsigned int’} may change value [-Wconversion]  
```
