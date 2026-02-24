# #465 - Test failure in ooura_fourier_transform_test with Apple Clang 12.0 and C++17 [Closed]

> Username: evanmiller  
> Created at: 2020-12-23 00:54:46 UTC  
> Updated at: 2021-01-18 17:18:49 UTC  
> Closed at: 2021-01-18 17:18:49 UTC  
> Url: https://github.com/boostorg/math/issues/465  

Full log here:  
  
https://github.com/evanmiller/math/runs/1597837166  
  
Relevant portion:  
  
```  
testing.capture-output ../../../bin.v2/libs/math/test/ooura_fourier_integral_test.test/clang-darwin-12.0/debug/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/ooura_fourier_integral_test.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
Testing integral of cos(x)/(x*x+1) on type float  
Testing integral of cos(x)/(x*x+1) on type double  
Testing integral of cos(x)/(x*x+1) on type long double  
Testing integral of exp(-a*x) on type float  
Testing integral of exp(-a*x) on type double  
Testing integral of exp(-a*x) on type long double  
ooura_fourier_integral_test.cpp:298: error: in "ooura_fourier_transform_test": difference{2.96692e-16} between Is{0.124999999999999962914} and exact{0.125} exceeds 1.08420217248550443401e-17  
  
...  
  
*** 1 failure is detected in the test module "test_ooura_fourier_transform"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-12-23 12:03:21 UTC  
> Url: https://github.com/boostorg/math/issues/465#issuecomment-750227922  

@NAThompson : Error rate is suspiciously close to double precision, so one would suspect a stray double constant or double-precision function call?

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-12-23 23:26:42 UTC  
> Url: https://github.com/boostorg/math/issues/465#issuecomment-750558735  

@evanmiller : Is your `long double` 64 bits? That would explain the problem . . .   
  
My apologies to everyone; I can't look at any of these until January . . .

---

## Comment 3

> Username: evanmiller  
> Created at: 2020-12-24 01:50:26 UTC  
> Url: https://github.com/boostorg/math/issues/465#issuecomment-750700561  

long double is 80 bits

---

## Comment 4

> Username: NAThompson  
> Created at: 2021-01-14 21:22:32 UTC  
> Url: https://github.com/boostorg/math/issues/465#issuecomment-760484675  

Should be fixed in [this commit](https://github.com/boostorg/math/commit/76631a42b611a49d897ce6a5190243e9f87b7bd8).

---

## Comment 5

> Username: evanmiller  
> Created at: 2021-01-14 22:01:52 UTC  
> Url: https://github.com/boostorg/math/issues/465#issuecomment-760503938  

@NAThompson The reported difference is nearly 30X the accepted tolerance (3e-16 vs 1e-17). I'll wait to see what the test says but a 5X tolerance bump won't be enough, will it?

---

## Comment 6

> Username: NAThompson  
> Created at: 2021-01-15 13:40:13 UTC  
> Url: https://github.com/boostorg/math/issues/465#issuecomment-760947723  

@evanmiller : Bah; I should've taken the opportunity to convert the test to using ULPs. . . I'll fix again if needed.

---

## Comment 7

> Username: evanmiller  
> Created at: 2021-01-15 13:44:34 UTC  
> Url: https://github.com/boostorg/math/issues/465#issuecomment-760949974  

@NAThompson Looks like it's needed...  
  
https://github.com/boostorg/math/runs/1704804139?check_suite_focus=true#step:12:603

---

## Comment 8

> Username: evanmiller  
> Created at: 2021-01-18 13:34:22 UTC  
> Url: https://github.com/boostorg/math/issues/465#issuecomment-762254342  

Here's some additional debug info I gathered by flipping on `BOOST_MATH_INSTRUMENT_OOURA` and logging the current values of A and Omega. You can see that a few of the long double integrals are going through several iterations.  
  
I will note that the one that the failing test is noticeably slower than the others. I don't understand the code well enough to debug it but maybe this will be useful to someone who can.  
  
```  
Testing integral of exp(-a*x) on type double  
A = 1  Omega = 1  
h = 0.125000000000000, I_h = 0.500000000000000 = 0x1p-1, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.500000000000000 = 0x1.ffffffffffffdp-2, absolute error estimate = 1.665334536937735e-16  
A = 1  Omega = 2  
h = 0.125000000000000, I_h = 0.200000000000000 = 0x1.999999999999dp-3, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.200000000000000 = 0x1.9999999999998p-3, absolute error estimate = 2.775557561562891e-16  
A = 1  Omega = 3  
h = 0.125000000000000, I_h = 0.100000000000000 = 0x1.9999999999999p-4, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.100000000000000 = 0x1.9999999999995p-4, absolute error estimate = 1.665334536937735e-16  
A = 1  Omega = 4  
h = 0.125000000000000, I_h = 0.058823529411765 = 0x1.e1e1e1e1e1e21p-5, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.058823529411765 = 0x1.e1e1e1e1e1e1dp-5, absolute error estimate = 1.110223024625157e-16  
A = 2  Omega = 1  
h = 0.125000000000000, I_h = 0.400000000000000 = 0x1.999999999999ap-2, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.400000000000000 = 0x1.999999999999bp-2, absolute error estimate = 5.551115123125783e-17  
A = 2  Omega = 2  
h = 0.125000000000000, I_h = 0.250000000000000 = 0x1p-2, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.250000000000000 = 0x1.ffffffffffffdp-3, absolute error estimate = 1.665334536937735e-16  
A = 2  Omega = 3  
h = 0.125000000000000, I_h = 0.153846153846154 = 0x1.3b13b13b13b13p-3, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.153846153846154 = 0x1.3b13b13b13b16p-3, absolute error estimate = 2.775557561562891e-16  
A = 2  Omega = 4  
h = 0.125000000000000, I_h = 0.100000000000000 = 0x1.999999999999dp-4, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.100000000000000 = 0x1.9999999999998p-4, absolute error estimate = 2.775557561562891e-16  
A = 3  Omega = 1  
h = 0.125000000000000, I_h = 0.300000000000000 = 0x1.3333333333334p-2, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.300000000000000 = 0x1.3333333333336p-2, absolute error estimate = 1.110223024625157e-16  
A = 3  Omega = 2  
h = 0.125000000000000, I_h = 0.230769230769231 = 0x1.d89d89d89d89fp-3, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.230769230769231 = 0x1.d89d89d89d89fp-3, absolute error estimate = 0.000000000000000e+00  
A = 3  Omega = 3  
h = 0.125000000000000, I_h = 0.166666666666667 = 0x1.5555555555555p-3, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.166666666666667 = 0x1.5555555555553p-3, absolute error estimate = 1.665334536937735e-16  
A = 3  Omega = 4  
h = 0.125000000000000, I_h = 0.120000000000000 = 0x1.eb851eb851ebap-4, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.120000000000000 = 0x1.eb851eb851eb9p-4, absolute error estimate = 5.551115123125783e-17  
A = 4  Omega = 1  
h = 0.125000000000000, I_h = 0.235294117647059 = 0x1.e1e1e1e1e1e2p-3, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.235294117647059 = 0x1.e1e1e1e1e1e1ap-3, absolute error estimate = 1.665334536937735e-16  
A = 4  Omega = 2  
h = 0.125000000000000, I_h = 0.200000000000000 = 0x1.999999999999ap-3, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.200000000000000 = 0x1.999999999999bp-3, absolute error estimate = 5.551115123125783e-17  
A = 4  Omega = 3  
h = 0.125000000000000, I_h = 0.160000000000000 = 0x1.47ae147ae147bp-3, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.160000000000000 = 0x1.47ae147ae147bp-3, absolute error estimate = 0.000000000000000e+00  
A = 4  Omega = 4  
h = 0.125000000000000, I_h = 0.125000000000000 = 0x1p-3, absolute error estimate = nan  
h = 0.062500000000000, I_h = 0.125000000000000 = 0x1.ffffffffffffdp-4, absolute error estimate = 1.665334536937735e-16  
Testing integral of exp(-a*x) on type long double  
A = 1  Omega = 1  
h = 0.062500000000000000, I_h = 0.500000000000000000 = 0x8p-4, absolute error estimate = nan  
h = 0.031250000000000000, I_h = 0.500000000000000000 = 0xf.ffffffffffffff8p-5, absolute error estimate = 2.168404344971008868e-19  
A = 1  Omega = 2  
h = 0.062500000000000000, I_h = 0.200000000000000000 = 0xc.cccccccccccccdp-6, absolute error estimate = nan  
h = 0.031250000000000000, I_h = 0.200000000000000000 = 0xc.cccccccccccccccp-6, absolute error estimate = 1.084202172485504434e-19  
A = 1  Omega = 3  
h = 0.062500000000000000, I_h = 0.100000000000000000 = 0xc.cccccccccccccccp-7, absolute error estimate = nan  
h = 0.031250000000000000, I_h = 0.100000000000000000 = 0xc.cccccccccccccd7p-7, absolute error estimate = 2.168404344971008868e-19  
A = 1  Omega = 4  
h = 0.062500000000000000, I_h = 0.058823529411764706 = 0xf.0f0f0f0f0f0f0ecp-8, absolute error estimate = nan  
h = 0.031250000000000000, I_h = 0.058823529411764706 = 0xf.0f0f0f0f0f0f101p-8, absolute error estimate = 2.846030702774449139e-19  
h = 0.015625000000000000, I_h = 0.058823529411764706 = 0xf.0f0f0f0f0f0f114p-8, absolute error estimate = 2.574980159653073031e-19  
h = 0.007812500000000000, I_h = 0.058823529411764706 = 0xf.0f0f0f0f0f0f101p-8, absolute error estimate = 2.574980159653073031e-19  
h = 0.003906250000000000, I_h = 0.058823529411764706 = 0xf.0f0f0f0f0f0f0f3p-8, absolute error estimate = 1.897353801849632760e-19  
A = 2  Omega = 1  
h = 0.007812500000000000, I_h = 0.400000000000000000 = 0xc.cccccccccccccc4p-5, absolute error estimate = nan  
h = 0.003906250000000000, I_h = 0.400000000000000000 = 0xc.cccccccccccccc1p-5, absolute error estimate = 8.131516293641283255e-20  
A = 2  Omega = 2  
h = 0.007812500000000000, I_h = 0.250000000000000000 = 0x8p-5, absolute error estimate = nan  
h = 0.003906250000000000, I_h = 0.250000000000000000 = 0xf.ffffffffffffff5p-6, absolute error estimate = 2.981555974335137194e-19  
A = 2  Omega = 3  
h = 0.007812500000000000, I_h = 0.153846153846153846 = 0x9.d89d89d89d89d85p-6, absolute error estimate = nan  
h = 0.003906250000000000, I_h = 0.153846153846153846 = 0x9.d89d89d89d89d83p-6, absolute error estimate = 8.131516293641283255e-20  
A = 2  Omega = 4  
h = 0.007812500000000000, I_h = 0.100000000000000000 = 0xc.cccccccccccccc6p-7, absolute error estimate = nan  
h = 0.003906250000000000, I_h = 0.100000000000000000 = 0xc.cccccccccccccbep-7, absolute error estimate = 2.168404344971008868e-19  
A = 3  Omega = 1  
h = 0.007812500000000000, I_h = 0.300000000000000000 = 0x9.99999999999998dp-5, absolute error estimate = nan  
h = 0.003906250000000000, I_h = 0.300000000000000000 = 0x9.99999999999998dp-5, absolute error estimate = 0.000000000000000000e+00  
A = 3  Omega = 2  
h = 0.007812500000000000, I_h = 0.230769230769230769 = 0xe.c4ec4ec4ec4ec4fp-6, absolute error estimate = nan  
h = 0.003906250000000000, I_h = 0.230769230769230769 = 0xe.c4ec4ec4ec4ec49p-6, absolute error estimate = 1.626303258728256651e-19  
A = 3  Omega = 3  
h = 0.007812500000000000, I_h = 0.166666666666666667 = 0xa.aaaaaaaaaaaaaabp-6, absolute error estimate = nan  
h = 0.003906250000000000, I_h = 0.166666666666666667 = 0xa.aaaaaaaaaaaaaa3p-6, absolute error estimate = 2.981555974335137194e-19  
A = 3  Omega = 4  
h = 0.007812500000000000, I_h = 0.120000000000000000 = 0xf.5c28f5c28f5c27dp-7, absolute error estimate = nan  
h = 0.003906250000000000, I_h = 0.120000000000000000 = 0xf.5c28f5c28f5c287p-7, absolute error estimate = 2.710505431213761085e-19  
A = 4  Omega = 1  
h = 0.007812500000000000, I_h = 0.235294117647058824 = 0xf.0f0f0f0f0f0f0f3p-6, absolute error estimate = nan  
h = 0.003906250000000000, I_h = 0.235294117647058823 = 0xf.0f0f0f0f0f0f0e4p-6, absolute error estimate = 2.032879073410320814e-19  
A = 4  Omega = 2  
h = 0.007812500000000000, I_h = 0.200000000000000000 = 0xc.cccccccccccccc4p-6, absolute error estimate = nan  
h = 0.003906250000000000, I_h = 0.200000000000000000 = 0xc.cccccccccccccc1p-6, absolute error estimate = 8.131516293641283255e-20  
A = 4  Omega = 3  
h = 0.007812500000000000, I_h = 0.160000000000000000 = 0xa.3d70a3d70a3d705p-6, absolute error estimate = nan  
h = 0.003906250000000000, I_h = 0.160000000000000000 = 0xa.3d70a3d70a3d6f7p-6, absolute error estimate = 5.421010862427522170e-19  
h = 0.001953125000000000, I_h = 0.160000000000000000 = 0xa.3d70a3d70a3d71p-6, absolute error estimate = 1.002887009549091601e-18  
h = 0.000976562500000000, I_h = 0.160000000000000000 = 0xa.3d70a3d70a3d701p-6, absolute error estimate = 5.963111948670274387e-19  
h = 0.000488281250000000, I_h = 0.160000000000000000 = 0xa.3d70a3d70a3d716p-6, absolute error estimate = 8.402566836762659364e-19  
h = 0.000244140625000000, I_h = 0.160000000000000000 = 0xa.3d70a3d70a3d6eep-6, absolute error estimate = 1.626303258728256651e-18  
h = 0.000122070312500000, I_h = 0.159999999999999999 = 0xa.3d70a3d70a3d6afp-6, absolute error estimate = 2.574980159653073031e-18  
h = 0.000061035156250000, I_h = 0.159999999999999999 = 0xa.3d70a3d70a3d6bbp-6, absolute error estimate = 5.149960319306146062e-19  
A = 4  Omega = 4  
h = 0.000122070312500000, I_h = 0.124999999999999999 = 0xf.fffffffffffff58p-7, absolute error estimate = nan  
h = 0.000061035156250000, I_h = 0.124999999999999998 = 0xf.fffffffffffff1ep-7, absolute error estimate = 1.572093150103981429e-18  
h = 0.000030517578125000, I_h = 0.124999999999999998 = 0xf.ffffffffffffee7p-7, absolute error estimate = 1.490777987167568597e-18  
h = 0.000015258789062500, I_h = 0.124999999999999995 = 0xf.ffffffffffffd05p-7, absolute error estimate = 1.306463617845032843e-17  
h = 0.000007629394531250, I_h = 0.124999999999999993 = 0xf.ffffffffffffc1ep-7, absolute error estimate = 6.261267546103788106e-18  
h = 0.000003814697265625, I_h = 0.124999999999999980 = 0xf.ffffffffffff432p-7, absolute error estimate = 5.496905014501507480e-17  
h = 0.000001907348632812, I_h = 0.124999999999999963 = 0xf.fffffffffffea9fp-7, absolute error estimate = 6.643448811904928419e-17  
ooura_fourier_integral_test.cpp:306: error: in "ooura_fourier_transform_test": difference{2.96692e-16} between Is{0.124999999999999962914} and exact{0.125} exceeds 5.42101086242752217004e-17  
```

---

## Comment 9

> Username: NAThompson  
> Created at: 2021-01-18 14:28:06 UTC  
> Url: https://github.com/boostorg/math/issues/465#issuecomment-762284579  

> I will note that the one that the failing test is noticeably slower than the others. I don't understand the code well enough to debug it but maybe this will be useful to someone who can.  
  
Thanks! This behavior is expected as the integrals become more challenging as the frequency increases. Looking through this log, it appears that everything is fine, but I'll double check.

---

## Comment 10

> Username: NAThompson  
> Created at: 2021-01-18 15:01:07 UTC  
> Url: https://github.com/boostorg/math/issues/465#issuecomment-762303847  

In 332bc10 I just reduced the maximum frequency tested rather than kick up the tolerance indefinitely.

---

## Comment 11

> Username: evanmiller  
> Created at: 2021-01-18 15:08:26 UTC  
> Url: https://github.com/boostorg/math/issues/465#issuecomment-762308021  

@NAThompson That will do it! Maybe restore the older, tighter tolerance since the failing test case is being eliminated? In any event I'll close the issue.

---

## Comment 12

> Username: NAThompson  
> Created at: 2021-01-18 16:30:00 UTC  
> Updated at: 2021-01-18 16:30:48 UTC  
> Url: https://github.com/boostorg/math/issues/465#issuecomment-762354379  

@evanmiller : Yeah, there's something to be said for that, but we have no reason to believe that the current tolerance is somehow "fundamental". If that test breaks in the future, we'll just need to kick it up again, and nothing is learned about the actual code.  
  
I'd really like to define a *useful* condition number for oscillatory integrals to give a reasonable error for these tests, but the standard techniques give an infinite condition number.

---

## Comment 13

> Username: evanmiller  
> Created at: 2021-01-18 17:18:49 UTC  
> Url: https://github.com/boostorg/math/issues/465#issuecomment-762379433  

Test is passing in the CI now, closing
