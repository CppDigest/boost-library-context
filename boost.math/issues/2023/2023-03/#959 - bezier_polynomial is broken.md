# #959 - bezier_polynomial is broken [Closed]

> Username: jayMcBee  
> Created at: 2023-03-02 08:57:01 UTC  
> Updated at: 2023-03-03 18:01:19 UTC  
> Closed at: 2023-03-03 15:55:39 UTC  
> Url: https://github.com/boostorg/math/issues/959  

Sorry for the harsh title :-)  
  
It appears the static storage - `static inline RandomAccessContainer& get_bezier_storage()` in `boost::math::interpolators::detail` - is affecting repeated calculations with different numbers of control points?  
  
I tried to figure out why my replacement of our home-grown bezier curve algorithm did not work after switching to boost::math in an app where I'm repeatedly calculating quadratic and cubic bezier curves.  
  
Eventually I could boil the error down to this test case:  
  
**Test Run 1 (1 invocation, 3 control points):**  
  
```  
09:41:28.856512+0100 Bezier[87372:1251113] repeatedlyCalculateBoostCurveWithControlPoints:withResolution:repeats: w/ 3 points  
 // points = {{0.0,0.0}, {0.0,100.0}, {100.0, 100.0}};  
  
09:41:28.857753+0100 Bezier[87372:1251113] {0.00, 0.00} @ 0.0  
09:41:28.858055+0100 Bezier[87372:1251113] {1.00, 19.00} @ 0.1  
09:41:28.858346+0100 Bezier[87372:1251113] {4.00, 36.00} @ 0.2  
09:41:28.858617+0100 Bezier[87372:1251113] {9.00, 51.00} @ 0.3  
09:41:28.858865+0100 Bezier[87372:1251113] {16.00, 64.00} @ 0.4  
09:41:28.859138+0100 Bezier[87372:1251113] {25.00, 75.00} @ 0.5  
09:41:28.859382+0100 Bezier[87372:1251113] {36.00, 84.00} @ 0.6  
09:41:28.859621+0100 Bezier[87372:1251113] {49.00, 91.00} @ 0.7  
09:41:28.859886+0100 Bezier[87372:1251113] {64.00, 96.00} @ 0.8  
09:41:28.860109+0100 Bezier[87372:1251113] {81.00, 99.00} @ 0.9  
09:41:28.860378+0100 Bezier[87372:1251113] {100.00, 100.00} @ 1.0  
```  
  
  
**Test Run 2 (2 invocations - 4, then 3 control points):**  
  
```  
09:40:38.720264+0100 Bezier[87334:1249927] repeatedlyCalculateBoostCurveWithControlPoints:withResolution:repeats: w/ 4 points  
 // points = {{0.0,0.0}, {0.0,25.0}, {0.0,75.0}, {100.0, 100.0}};  
  
09:40:38.720983+0100 Bezier[87334:1249927] {0.00, 0.00} @ 0.0  
09:40:38.721208+0100 Bezier[87334:1249927] {0.10, 8.20} @ 0.1  
09:40:38.721346+0100 Bezier[87334:1249927] {0.80, 17.60} @ 0.2  
09:40:38.721453+0100 Bezier[87334:1249927] {2.70, 27.90} @ 0.3  
09:40:38.721557+0100 Bezier[87334:1249927] {6.40, 38.80} @ 0.4  
09:40:38.721684+0100 Bezier[87334:1249927] {12.50, 50.00} @ 0.5  
09:40:38.721800+0100 Bezier[87334:1249927] {21.60, 61.20} @ 0.6  
09:40:38.721898+0100 Bezier[87334:1249927] {34.30, 72.10} @ 0.7  
09:40:38.722020+0100 Bezier[87334:1249927] {51.20, 82.40} @ 0.8  
09:40:38.722157+0100 Bezier[87334:1249927] {72.90, 91.80} @ 0.9  
09:40:38.722265+0100 Bezier[87334:1249927] {100.00, 100.00} @ 1.0  
  
  
09:40:38.722428+0100 Bezier[87334:1249927] repeatedlyCalculateBoostCurveWithControlPoints:withResolution:repeats: w/ 3 points  
 // points = {{0.0,0.0}, {0.0,100.0}, {100.0, 100.0}};  
  
09:40:38.722607+0100 Bezier[87334:1249927] {0.00, 0.00} @ 0.0  
09:40:38.728200+0100 Bezier[87334:1249927] {2.80, 27.10} @ 0.1  
09:40:38.728349+0100 Bezier[87334:1249927] {10.40, 48.80} @ 0.2  
09:40:38.728463+0100 Bezier[87334:1249927] {21.60, 65.70} @ 0.3  
09:40:38.728586+0100 Bezier[87334:1249927] {35.20, 78.40} @ 0.4  
09:40:38.728699+0100 Bezier[87334:1249927] {50.00, 87.50} @ 0.5  
09:40:38.728802+0100 Bezier[87334:1249927] {64.80, 93.60} @ 0.6  
09:40:38.728897+0100 Bezier[87334:1249927] {78.40, 97.30} @ 0.7  
09:40:38.729018+0100 Bezier[87334:1249927] {89.60, 99.20} @ 0.8  
09:40:38.729151+0100 Bezier[87334:1249927] {97.20, 99.90} @ 0.9  
09:40:38.729261+0100 Bezier[87334:1249927] {100.00, 100.00} @ 1.0  
```  
  
  
For each bezier curve calculation a new `bezier_polynomial` instance is instantiated.  
  
Please advise if this is a bug - or user error..?  
Thanks!  
  
  
_Test environment: Boost 1.81, Xcode 14.2 (14C18) on macOS 12.6.3 (Intel), Apple clang version 14.0.0 (clang-1400.0.29.202)_

---

## Comment 1

> Username: jayMcBee  
> Created at: 2023-03-02 11:07:20 UTC  
> Updated at: 2023-03-02 12:45:42 UTC  
> Url: https://github.com/boostorg/math/issues/959#issuecomment-1451691887  

This is a minimal sample for replicating the issue, slightly modified code sample of the original Bezier Polynomials documentation (https://www.boost.org/doc/libs/1_81_0/libs/math/doc/html/math_toolkit/bezier_polynomial.html):  
  
  
  
```  
#include <iostream>  
#include <array>  
#include <vector>  
#include <boost/math/interpolators/bezier_polynomial.hpp>  
  
  
typedef std::vector<std::array<double, 3>> tControlPoints;  
  
  
void interpolateWithPoints(tControlPoints cp)  
{  
        const auto cpSize = cp.size();  
	auto bp = boost::math::interpolators::bezier_polynomial(std::move(cp));  
  
	// Interpolate at t = 0.5:  
	std::array<double, 3> point = bp(0.5);  
        std::cout << cpSize << " points, t = 0.5:\n";  
  
  
	for (const auto& c : point) {  
		std::cout << "  " << c << "\n";  
	}  
}  
  
  
int main(int argc, const char * argv[])  
{  
	auto cp3 = tControlPoints{{0,0,0}, {1,0,0}, {0,1,0}};  
	interpolateWithPoints(cp3);  
  
	auto cp4 = tControlPoints{{0,0,0}, {1,0,0}, {0,1,0}, {0,0,1}};  
	interpolateWithPoints(cp4);  
  
	auto cp3b = tControlPoints{{0,0,0}, {1,0,0}, {0,1,0}};  
	interpolateWithPoints(cp3b);  
  
	return 0;  
}  
  
```

---

## Comment 2

> Username: jayMcBee  
> Created at: 2023-03-02 11:08:14 UTC  
> Updated at: 2023-03-02 12:45:56 UTC  
> Url: https://github.com/boostorg/math/issues/959#issuecomment-1451693064  

Output when running the sample provided:  
  
```  
3 points, t = 0.5:  
  0.5  
  0.25  
  0  
  
4 points, t = 0.5:  
  0.375  
  0.375  
  0.125  
  
3 points, t = 0.5:  
  0.375  
  0.375  
  0.125  
```

---

## Comment 3

> Username: MarekR22  
> Created at: 2023-03-02 13:05:35 UTC  
> Updated at: 2023-03-02 13:18:24 UTC  
> Url: https://github.com/boostorg/math/issues/959#issuecomment-1451837987  

This is bug in boost.  
There is a global state: https://github.com/boostorg/math/blame/051a1bfae97ce8c7dfcec475f78c207037d7f3e4/include/boost/math/interpolators/detail/bezier_polynomial_detail.hpp#L16-L21  
Which is used to cache some data.  
  
So it is impossible to have two bezier interpolation at the same time.  
This global state should be moved to be a (none static) field of `bezier_polynomial_imp`

---

## Comment 4

> Username: jzmaddock  
> Created at: 2023-03-02 17:35:41 UTC  
> Url: https://github.com/boostorg/math/issues/959#issuecomment-1452255839  

Confirmed, making the scratch space static is fine, but this line: https://github.com/boostorg/math/blob/8cfdbb6d2e96bd521300db3df404fca9ce68a29b/include/boost/math/interpolators/detail/bezier_polynomial_detail.hpp#L76  should be:  
  
```  
        decasteljau_recursion(scratch_space, control_points_.size() - 1, t);  
```  
  
Given that scratch_space may have too much storage.  
  
@NAThompson ?

---

## Comment 5

> Username: mborland  
> Created at: 2023-03-02 17:36:02 UTC  
> Url: https://github.com/boostorg/math/issues/959#issuecomment-1452256287  

@jayMcBee Can you give the linked PR a try?

---

## Comment 6

> Username: jzmaddock  
> Created at: 2023-03-02 17:37:16 UTC  
> Url: https://github.com/boostorg/math/issues/959#issuecomment-1452258219  

Ha, we've been working in parallel again, @mborland can you check the diagnosis above as it preserves the static/cached scratch storage?

---

## Comment 7

> Username: mborland  
> Created at: 2023-03-02 17:48:11 UTC  
> Url: https://github.com/boostorg/math/issues/959#issuecomment-1452271417  

@jzmaddock That seems to work fine. Updated the linked PR.

---

## Comment 8

> Username: jayMcBee  
> Created at: 2023-03-03 09:19:41 UTC  
> Url: https://github.com/boostorg/math/issues/959#issuecomment-1453218262  

The PR works fine in my test case and in the original app!  
Thanks for the quick fix 👍

---

## Comment 9

> Username: NAThompson  
> Created at: 2023-03-03 15:55:36 UTC  
> Url: https://github.com/boostorg/math/issues/959#issuecomment-1453739592  

@jayMcBee : Sincerest apologies for my stupidity, and big thanks to @jzmaddock and @mborland for seeing it so quickly.

---

## Comment 10

> Username: jayMcBee  
> Created at: 2023-03-03 16:03:04 UTC  
> Updated at: 2023-03-03 16:03:27 UTC  
> Url: https://github.com/boostorg/math/issues/959#issuecomment-1453751583  

> @jayMcBee : Sincerest apologies for my stupidity, and big thanks to @jzmaddock and @mborland for seeing it so quickly.  
  
No worries - always great to delete custom, in-house code and switch to Boost or Standard Library algorithms!  
Did the fix still make the 1.82 deadline..?

---

## Comment 11

> Username: mborland  
> Created at: 2023-03-03 16:08:08 UTC  
> Url: https://github.com/boostorg/math/issues/959#issuecomment-1453758582  

Yes. Once the PR finishes cycling in CI (it looks hung so I sent the manager a message) I will merge it into develop and master. The beta for 1.82 should be out on the [15 of March](https://www.boost.org/development/index.html).

---

## Comment 12

> Username: jayMcBee  
> Created at: 2023-03-03 16:56:31 UTC  
> Url: https://github.com/boostorg/math/issues/959#issuecomment-1453819841  

> Yes. Once the PR finishes cycling in CI (it looks hung so I sent the manager a message) I will merge it into develop and master. The beta for 1.82 should be out on the [15 of March](https://www.boost.org/development/index.html).  
  
Awesome 🍻

---

## Comment 13

> Username: mborland  
> Created at: 2023-03-03 18:01:18 UTC  
> Url: https://github.com/boostorg/math/issues/959#issuecomment-1453898364  

Merged both so you should see this soon.
