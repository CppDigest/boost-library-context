# #17 - Question about the check for underflow in safe_fpt_division() [Closed]

> Username: BinCaoWR  
> Created at: 2019-07-25 07:02:15 UTC  
> Updated at: 2020-06-04 04:12:03 UTC  
> Closed at: 2020-06-04 04:12:03 UTC  
> Url: https://github.com/boostorg/qvm/issues/17  

I got the following test failure:  
../libs/qvm/test/rot_mat_test.cpp(120): BOOST_QVM_TEST_CLOSE(m1.a,m2.a,0.0002f)   
failed in function void {anonymous}::test_xyz() [with int D = 3]  
-0.821818       -0.821818  
0.448961        0.448961  
-0.350783       -0.350783  
-4.74575e-06    -4.75245e-06  
0.615674        0.615674  
0.788001        0.788001  
0.56975 0.56975  
0.647595        0.647595  
-0.50597        -0.50597  
  
I investigated the issue and noticed the check for underflow in safe_fpt_division(https://github.com/boostorg/test/blob/develop/include/boost/test/tools/floating_point_comparison.hpp#L163).  
  
Looks like the check is not enough for the value(-4.74575e-06    -4.75245e-06).  
Shouldn't "if (f1 == static_cast<FPT>(0))" be changed to "if (f1 < 0.000001)" for float type?

---

## Comment 1

> Username: zajo  
> Created at: 2019-07-25 23:19:59 UTC  
> Url: https://github.com/boostorg/qvm/issues/17#issuecomment-515249472  

I'm actually not very familiar with the floating point comparison library. I see that I'm using:  
  
```  
boost::math::fpc::close_at_tolerance<float>(tolerance,boost::math::fpc::FPC_STRONG)(a,b);  
```  
  
Maybe the correct thing to do is change this to `math::fpc::FPC_WEAK`?  
  
Which platform is this on? What compiler? Version?

---

## Comment 2

> Username: BinCaoWR  
> Created at: 2019-07-26 02:12:33 UTC  
> Url: https://github.com/boostorg/qvm/issues/17#issuecomment-515281363  

Thanks for your quick response!  
  
It is on PPC, gcc-8.1.0, c++14.  
  
Change FPC_STRONG to FPC_WEAK does not fix the issue.  
  
|((-4.74575e-06) - (-4.75245e-06))|/4.74575e-06 = 0.001411  
|((-4.74575e-06) - (-4.75245e-06))|/4.75245e-06 = 0.001409  
  
Both max_rel_diff and min_rel_diff are larger than the tolerance(0.0002f).  
  
I think the check "if (f1 == static_cast(0))" in safe_fpt_division() is not safe.  
  
What do you think?

---

## Comment 3

> Username: zajo  
> Created at: 2020-06-04 04:12:03 UTC  
> Url: https://github.com/boostorg/qvm/issues/17#issuecomment-638591008  

I apologize this slipped through the cracks and I have not looked at it. I am going to close it now but please free to reopen if this is still an issue, I do want to fix it.
