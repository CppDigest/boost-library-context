# #162 - BOOST_CHECK_CLOSE(0, smallnumber) fails [Closed]

> Username: WilliamTambellini  
> Created at: 2018-09-14 20:25:40 UTC  
> Updated at: 2019-04-06 08:15:59 UTC  
> Closed at: 2019-02-07 19:53:01 UTC  
> Url: https://github.com/boostorg/test/issues/162  

Hello  
With boost 1.60   
BOOST_CHECK_CLOSE(-0, -4.37113883e-08, 1)   
fails with :  
difference{3.40282e+38} between -0 and -4.37113883e-08 exceeds 1%  
Note the difference is  super big.  
Should nt it pass ?  
Tks

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-09-14 20:35:40 UTC  
> Url: https://github.com/boostorg/test/issues/162#issuecomment-421477169  

Hi,  
  
There were many bug fixes since 1.60 (see [here](https://www.boost.org/doc/libs/1_68_0/libs/test/doc/html/boost_test/change_log.html) for more details).  
  
Would you please try again with the latest version of Boost?

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-02-07 19:53:01 UTC  
> Url: https://github.com/boostorg/test/issues/162#issuecomment-461572408  

Sorry for the late investigation. It turns out that you cannot use `BOOST_CHECK_CLOSE` to compare to the value `0`, as it would use the algorithm described [here](https://www.boost.org/doc/libs/1_69_0/libs/test/doc/html/boost_test/testing_tools/extended_comparison/floating_point/floating_points_comparison_theory.html).  
  
You have 2 options:  
  
* either use `BOOST_CHECK_SMALL(-4.37113883e-08, 1.)` (note `.` in the `1.`)  
* or use `BOOST_TEST(-4.37113883e-08 == 0, 1. % tt::tolerance())` (for percentage tolerance)  
  
Hope that helps!

---

## Comment 3

> Username: WilliamTambellini  
> Created at: 2019-04-04 23:39:21 UTC  
> Url: https://github.com/boostorg/test/issues/162#issuecomment-480100635  

Thank you @raffienficiaud   
I have retried with 1.69 and the same behavior.   
Could nt find a better perf than writing my own CHECK macro doing :  
CHECK_CLOSE(result, ref)  
    if (result == 0 || ref == 0)  
      BOOST_CHECK_SMALL(result - ref, 0.01f);  
    else  
      BOOST_CHECK_CLOSE(result, ref, 0.01f);  
  
Mathematically speaking, it is legit to wonder if 0.0000001 is "close" to 0 (within a range).   
Should nt it be illogic to expect BOOST_CHECK_CLOSE() to work even if left or right are 0 ?

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2019-04-06 08:15:59 UTC  
> Url: https://github.com/boostorg/test/issues/162#issuecomment-480485273  

If you look at the equations for comparison [here](https://www.boost.org/doc/libs/1_69_0/libs/test/doc/html/boost_test/testing_tools/extended_comparison/floating_point/floating_points_comparison_theory.html), then only equation (1) can work when one of the arguments `u` or `v` is `0`. This is why there exists `BOOST_CHECK_SMALL` and `BOOST_CHECK_CLOSE`.  
  
On the other hand, this is done automatically with `BOOST_TEST` if   
  
* one of the arguments is floating point (since 1.70, otherwise the two arguments should be floating points)   
* and one of the argument is zero,   
  
so you should not be needing to write your own logic.
