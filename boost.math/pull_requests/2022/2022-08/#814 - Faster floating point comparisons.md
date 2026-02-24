# #814 Faster floating point comparisons [Open]

> Username: mborland  
> Created at: 2022-08-05 19:29:12 UTC  
> Updated at: 2023-04-13 12:15:05 UTC  
> Url: https://github.com/boostorg/math/pull/814  



---

## Comment 1

> Username: mborland  
> Created_at: 2022-08-07 15:42:34 UTC  
> Url: https://github.com/boostorg/math/pull/814#issuecomment-1207434147  

@NAThompson Since you got this to work previously for 128 bit float do you see anything obvious I am missing? Error for incomplete type [here](https://github.com/boostorg/math/runs/7708438647?check_suite_focus=true#step:13:7155).

---

## Comment 2

> Username: NAThompson  
> Created_at: 2022-08-07 20:57:33 UTC  
> Url: https://github.com/boostorg/math/pull/814#issuecomment-1207484103  

> Since you got this to work previously for 128 bit float do you see anything obvious I am missing?  
  
IIRC, this was something I looked up in the [quadmath manual](https://gcc.gnu.org/onlinedocs/libquadmath/). I couldn't find it though . . .

---

## Comment 3

> Username: mborland  
> Created_at: 2022-08-23 14:19:40 UTC  
> Url: https://github.com/boostorg/math/pull/814#issuecomment-1224144945  

@NAThompson This is good for review. I pulled 128 bit support into it's own header otherwise you would have to link quadmath anytime you used `next.hpp`.

---

## Comment 4

> Username: SiliconA-Z  
> Created_at: 2022-10-13 23:40:12 UTC  
> Url: https://github.com/boostorg/math/pull/814#issuecomment-1278288012  

Any updates on this?

---

## Review 5 [Commented]

> Username: NAThompson  
> Created_at: 2022-10-14 02:29:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/814#pullrequestreview-1141730823  

📁 include/boost/math/special_functions/fast_float_distance.hpp

```diff
  27 |+ // https://randomascii.wordpress.com/2012/01/23/stupid-float-tricks-2/
  28 |+ // https://blog.regehr.org/archives/959
  29 |+ inline std::int32_t fast_float_distance(float a, float b)
```

> Username: NAThompson  
> Created_at: 2022-10-14 02:29:24 UTC  
> Url: https://github.com/boostorg/math/pull/814#discussion_r995280676  

I think float distance can overflow an int32 from two floats right?

> Username: mborland  
> Created_at: 2022-10-14 02:47:51 UTC  
> Url: https://github.com/boostorg/math/pull/814#discussion_r995289231  

Yes. For all the edge cases I defer to John's implementation which is tested [here](https://github.com/boostorg/math/blob/develop/test/test_next.cpp#L192) to avoid this issue.

> Username: NAThompson  
> Created_at: 2022-10-14 03:00:52 UTC  
> Url: https://github.com/boostorg/math/pull/814#discussion_r995294232  

Well, I might just let the return type be a `std::int64_t`; seems like very little loss of performance.

> Username: mborland  
> Created_at: 2022-10-14 03:09:55 UTC  
> Url: https://github.com/boostorg/math/pull/814#discussion_r995298577  

I think that will emit a widening warning since we have to `memcpy` `float` into `std::int32_t`.

> Username: NAThompson  
> Created_at: 2022-10-14 04:12:00 UTC  
> Url: https://github.com/boostorg/math/pull/814#discussion_r995324179  

How about something like  
  
```  
int32_t i,j;  
memcpy(&j, &a);  
memcpy(&i, &b);  
return int64_t(i) - int64_t(j);  
```

> Username: mborland  
> Created_at: 2022-10-14 15:10:26 UTC  
> Updated_at: 2022-10-14 15:11:03 UTC  
> Url: https://github.com/boostorg/math/pull/814#discussion_r995868411  

That would work for `float`, but the logic is shared with the `double` case. The same issue would occur, and I don't want to use `__int128_t` in that case.

> Username: NAThompson  
> Created_at: 2022-10-15 17:12:36 UTC  
> Url: https://github.com/boostorg/math/pull/814#discussion_r996331701  

Yes, you care correct.


---

## Comment 6

> Username: mborland  
> Created_at: 2022-10-15 22:35:09 UTC  
> Url: https://github.com/boostorg/math/pull/814#issuecomment-1279844345  

Here are the before and after benchmarks @NAThompson :  
  
```  
Original performance (Boost 1.80.0):  
  
 Unable to determine clock rate from sysctl: hw.cpufrequency: No such file or directory  
 This does not affect benchmark measurements, only the metadata output.  
 2022-10-15T15:24:07-07:00  
 Running ./new_next_performance  
 Run on (10 X 24.0916 MHz CPU s)  
 CPU Caches:  
   L1 Data 64 KiB  
   L1 Instruction 128 KiB  
   L2 Unified 4096 KiB (x10)  
 Load Average: 1.86, 2.53, 5.83  
 ---------------------------------------------------------------------------------  
 Benchmark                                       Time             CPU   Iterations  
 ---------------------------------------------------------------------------------  
 float_distance<float>/2/real_time            61.4 ns         61.4 ns      9074469  
 float_distance<float>/4/real_time            61.7 ns         61.7 ns     11384150  
 float_distance<float>/8/real_time            61.4 ns         61.4 ns     10814604  
 float_distance<float>/16/real_time           61.7 ns         61.7 ns     11348376  
 float_distance<float>/32/real_time           61.4 ns         61.4 ns     11387167  
 float_distance<float>/64/real_time           61.6 ns         61.6 ns     11131932  
 float_distance<float>/128/real_time          61.4 ns         61.4 ns     11382029  
 float_distance<float>/256/real_time          61.4 ns         61.4 ns     11307649  
 float_distance<float>/512/real_time          61.4 ns         61.4 ns     11376048  
 float_distance<float>/1024/real_time         61.4 ns         61.4 ns     11355748  
 float_distance<float>/2048/real_time         61.8 ns         61.8 ns     11373776  
 float_distance<float>/4096/real_time         61.4 ns         61.4 ns     11382368  
 float_distance<float>/8192/real_time         61.4 ns         61.4 ns     11353453  
 float_distance<float>/16384/real_time        61.4 ns         61.4 ns     11378298  
 float_distance<float>/real_time_BigO        61.48 (1)       61.47 (1)  
 float_distance<float>/real_time_RMS             0 %             0 %  
 float_distance<double>/2/real_time           55.6 ns         55.6 ns     12580218  
 float_distance<double>/4/real_time           55.6 ns         55.6 ns     12577835  
 float_distance<double>/8/real_time           55.6 ns         55.6 ns     12564909  
 float_distance<double>/16/real_time          56.2 ns         56.2 ns     12554909  
 float_distance<double>/32/real_time          56.0 ns         56.0 ns     12544381  
 float_distance<double>/64/real_time          55.6 ns         55.6 ns     12566488  
 float_distance<double>/128/real_time         55.6 ns         55.6 ns     12499581  
 float_distance<double>/256/real_time         55.6 ns         55.6 ns     12565661  
 float_distance<double>/512/real_time         56.1 ns         56.1 ns     12550023  
 float_distance<double>/1024/real_time        55.8 ns         55.8 ns     12568603  
 float_distance<double>/2048/real_time        55.6 ns         55.6 ns     12546049  
 float_distance<double>/4096/real_time        55.6 ns         55.6 ns     12528525  
 float_distance<double>/8192/real_time        55.9 ns         55.9 ns     12563030  
 float_distance<double>/16384/real_time       56.0 ns         56.0 ns     12447644  
 float_distance<double>/real_time_BigO       55.78 (1)       55.78 (1)  
 float_distance<double>/real_time_RMS            0 %             0 %  
  
 New performance:  
  
 Unable to determine clock rate from sysctl: hw.cpufrequency: No such file or directory  
 This does not affect benchmark measurements, only the metadata output.  
 2022-10-15T15:31:37-07:00  
 Running ./new_next_performance  
 Run on (10 X 24.122 MHz CPU s)  
 CPU Caches:  
   L1 Data 64 KiB  
   L1 Instruction 128 KiB  
   L2 Unified 4096 KiB (x10)  
 Load Average: 2.12, 2.17, 4.26  
 ---------------------------------------------------------------------------------  
 Benchmark                                       Time             CPU   Iterations  
 ---------------------------------------------------------------------------------  
 float_distance<float>/2/real_time            15.8 ns         15.8 ns     42162717  
 float_distance<float>/4/real_time            15.9 ns         15.9 ns     44213877  
 float_distance<float>/8/real_time            15.8 ns         15.8 ns     43972542  
 float_distance<float>/16/real_time           15.8 ns         15.8 ns     44209456  
 float_distance<float>/32/real_time           15.8 ns         15.8 ns     44200244  
 float_distance<float>/64/real_time           15.8 ns         15.8 ns     44239293  
 float_distance<float>/128/real_time          15.8 ns         15.8 ns     44171202  
 float_distance<float>/256/real_time          15.8 ns         15.8 ns     44241507  
 float_distance<float>/512/real_time          15.9 ns         15.8 ns     44230034  
 float_distance<float>/1024/real_time         15.8 ns         15.8 ns     44241554  
 float_distance<float>/2048/real_time         15.8 ns         15.8 ns     44220802  
 float_distance<float>/4096/real_time         15.8 ns         15.8 ns     44220441  
 float_distance<float>/8192/real_time         15.9 ns         15.9 ns     44213994  
 float_distance<float>/16384/real_time        15.8 ns         15.8 ns     44215413  
 float_distance<float>/real_time_BigO        15.83 (1)       15.83 (1)  
 float_distance<float>/real_time_RMS             0 %             0 %  
 float_distance<double>/2/real_time           15.5 ns         15.5 ns     45098165  
 float_distance<double>/4/real_time           15.6 ns         15.6 ns     45065465  
 float_distance<double>/8/real_time           15.5 ns         15.5 ns     45058733  
 float_distance<double>/16/real_time          15.8 ns         15.7 ns     45078404  
 float_distance<double>/32/real_time          15.5 ns         15.5 ns     44832734  
 float_distance<double>/64/real_time          15.5 ns         15.5 ns     45077303  
 float_distance<double>/128/real_time         15.5 ns         15.5 ns     45067255  
 float_distance<double>/256/real_time         15.5 ns         15.5 ns     45073844  
 float_distance<double>/512/real_time         15.6 ns         15.6 ns     45109342  
 float_distance<double>/1024/real_time        15.5 ns         15.5 ns     44845180  
 float_distance<double>/2048/real_time        15.5 ns         15.5 ns     45051846  
 float_distance<double>/4096/real_time        15.5 ns         15.5 ns     45064317  
 float_distance<double>/8192/real_time        15.5 ns         15.5 ns     45115653  
 float_distance<double>/16384/real_time       15.5 ns         15.5 ns     45067642  
 float_distance<double>/real_time_BigO       15.54 (1)       15.54 (1)  
 float_distance<double>/real_time_RMS            0 %             0 %  
```

---

## Review 7 [Commented]

> Username: NAThompson  
> Created_at: 2022-10-15 22:35:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/814#pullrequestreview-1143233256  

📁 test/test_fast_float_distance.cpp

```diff
  27 |+     BOOST_MATH_ASSERT(fast_float_distance(float_advance(val, 4), val) == -4);
  28 |+     BOOST_MATH_ASSERT(fast_float_distance(float_advance(val, -4), val) == 4);
  29 |+     if(std::numeric_limits<T>::is_specialized && (std::numeric_limits<T>::has_denorm == std::denorm_present))
```

> Username: NAThompson  
> Created_at: 2022-10-15 22:35:58 UTC  
> Url: https://github.com/boostorg/math/pull/814#discussion_r996361903  

Doesn't the fast float distance unconditionally require denorm support?

> Username: mborland  
> Created_at: 2022-10-15 22:48:39 UTC  
> Url: https://github.com/boostorg/math/pull/814#discussion_r996362773  

It's just type punning so as long as the floating point and fixed width integer are the same number of bits you should be fine. I don't know of a platform without denorm support to test on.


---

## Comment 8

> Username: NAThompson  
> Created_at: 2022-10-15 22:37:10 UTC  
> Url: https://github.com/boostorg/math/pull/814#issuecomment-1279844597  

@mborland : Does it need a `fast_float_distance` header? Could we not just improve the performance of the current implementation?  
  
@jzmaddock : This is looking like it's about ready to go; might want to take a look.

---

## Comment 9

> Username: mborland  
> Created_at: 2022-10-15 22:40:03 UTC  
> Url: https://github.com/boostorg/math/pull/814#issuecomment-1279844984  

> @mborland : Does it need a `fast_float_distance` header? Could we not just improve the performance of the current implementation?  
  
The float and double cases improve upon the current implementation. I could not get the `__float128` case to work without forcing the user to link `-lquadmath` if using GCC which would be an unwelcome breaking change.

---

## Comment 10

> Username: NAThompson  
> Created_at: 2022-10-16 05:03:08 UTC  
> Url: https://github.com/boostorg/math/pull/814#issuecomment-1279892856  

>  I could not get the __float128 case to work without forcing the user to link -lquadmath if using GCC which would be an unwelcome breaking change.  
  
Wait, I thought we had to link `libquadmath` to use `__float128`  . . .

---

## Review 11 [Commented]

> Username: Flamefire  
> Created_at: 2022-12-31 16:23:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/814#pullrequestreview-1233364047  

📁 include/boost/math/special_functions/fast_float_distance.hpp

```diff
  57 |+     if (!(isfinite)(a))
  58 |+     {  
  59 |+         BOOST_MATH_THROW_EXCEPTION(std::domain_error("Both arguments to fast_float_distnace must be finite"));
```

> Username: Flamefire  
> Created_at: 2022-12-31 16:23:14 UTC  
> Url: https://github.com/boostorg/math/pull/814#discussion_r1059660839  

Typo:  
```diff  
-        BOOST_MATH_THROW_EXCEPTION(std::domain_error("Both arguments to fast_float_distnace must be finite"));  
+        BOOST_MATH_THROW_EXCEPTION(std::domain_error("Both arguments to fast_float_distance must be finite"));  
```

> Username: mborland  
> Created_at: 2022-12-31 16:34:03 UTC  
> Url: https://github.com/boostorg/math/pull/814#discussion_r1059661554  

Good catch. Thanks.


---

## Comment 12

> Username: NAThompson  
> Created_at: 2023-03-02 02:57:29 UTC  
> Url: https://github.com/boostorg/math/pull/814#issuecomment-1451201312  

>  I could not get the __float128 case to work without forcing the user to link -lquadmath if using GCC which would be an unwelcome breaking change.  
  
Could we use a judicious `__has_include` to workaround?  
  
Also, 99% of the value will be from `float` and `double` . . .

---

## Comment 13

> Username: mborland  
> Created_at: 2023-03-02 03:00:04 UTC  
> Url: https://github.com/boostorg/math/pull/814#issuecomment-1451206064  

Having the include isn't enough in this case. You have to explicitly link to -lquadmath.

---

## Comment 14

> Username: mborland  
> Created_at: 2023-04-13 12:15:05 UTC  
> Url: https://github.com/boostorg/math/pull/814#issuecomment-1506862487  

@jzmaddock Can you please take a look at this one? @NAThompson hit me up about merging this.

---
