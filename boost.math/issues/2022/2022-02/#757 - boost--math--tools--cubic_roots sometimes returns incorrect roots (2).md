# #757 - boost::math::tools::cubic_roots sometimes returns incorrect roots (2) [Closed]

> Username: SATovstun  
> Created at: 2022-02-06 00:33:41 UTC  
> Updated at: 2022-02-14 01:57:33 UTC  
> Closed at: 2022-02-14 01:57:33 UTC  
> Url: https://github.com/boostorg/math/issues/757  

Minimal working example:  
```  
#include <iostream>  
#include <boost/math/tools/cubic_roots.hpp>  
  
int main()  
{  
	using boost::math::tools::cubic_roots;  
	  
	std::cout << "((x+1)^2+1)*(x+1) = x^3 + 3*x^2 + 4*x + 2 = 0:\n";  
	std::cout << "True root: -1\n";  
	auto roots1 = cubic_roots<double>(1, 3, 4, 2);  
	std::cout << "Boost roots: " << roots1[0] << ", " << roots1[1] << ", " << roots1[2] << "\n\n";  
	  
	std::cout << "x^3 + 10000*x^2 + 200*x + 1 = 0:\n";  
	std::cout << "True roots (rounded): -9999.98, -0.01001, -0.00999\n";  
	auto roots2 = cubic_roots<double>(1, 10000, 200, 1);  
	std::cout << "Boost roots: " << roots2[0] << ", " << roots2[1] << ", " << roots2[2] << "\n";  
	  
	return 0;  
}  
```  
Output:  
```  
((x+1)^2+1)*(x+1) = x^3 + 3*x^2 + 4*x + 2 = 0:  
True root: -1  
Boost roots: -1, -1, -1  
  
x^3 + 10000*x^2 + 200*x + 1 = 0:  
True roots (rounded): -9999.98, -0.01001, -0.00999  
Boost roots: -9999.98, -3413.62, -3413.62  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2022-02-06 03:31:05 UTC  
> Updated at: 2022-02-06 03:31:17 UTC  
> Url: https://github.com/boostorg/math/issues/757#issuecomment-1030744194  

Looks legit, will investigate.

---

## Comment 2

> Username: fredbr  
> Created at: 2022-02-11 16:42:12 UTC  
> Url: https://github.com/boostorg/math/issues/757#issuecomment-1036400779  

Did some digging of my own. This issue seems to be related to numerical precision. When running the second example the branch that checks if there are 3 different roots evaluates to false.  
  
    Real p = b/a;  
    Real q = c/a;  
    Real r = d/a;  
    Real Q = (p*p - 3*q)/9;  
    Real R = (2*p*p*p - 9*p*q + 27*r)/54;  
    if (R*R < Q*Q*Q) {  
        Real rtQ = sqrt(Q);  
  
The code evaluates `R*R == Q*Q*Q` as true, because of precision errors, when in reality `R:R < Q:Q:Q` (a:b denoting the infinite precision multiplication of a and b).

---

## Comment 3

> Username: fredbr  
> Created at: 2022-02-11 16:47:29 UTC  
> Updated at: 2022-02-11 17:18:45 UTC  
> Url: https://github.com/boostorg/math/issues/757#issuecomment-1036405963  

The code suggests implementing this paper, _Algorithm 954: An Accurate and Efficient Cubic and Quartic Equation Solver for Physical Applications_.  
  
Maybe that algorithm fixes it? I will have a go at it

---

## Comment 4

> Username: SATovstun  
> Created at: 2022-02-11 18:31:56 UTC  
> Url: https://github.com/boostorg/math/issues/757#issuecomment-1036500697  

Yes, Algorithm 954 is indeed very accurate.

---

## Comment 5

> Username: NAThompson  
> Created at: 2022-02-12 19:35:29 UTC  
> Updated at: 2022-02-12 19:43:06 UTC  
> Url: https://github.com/boostorg/math/issues/757#issuecomment-1037418854  

It's actually a bug in the root polishing. You can see this by putting print statement before the root polishing and after. Should have a fix relatively soon.

---

## Comment 6

> Username: NAThompson  
> Created at: 2022-02-12 21:16:35 UTC  
> Updated at: 2022-02-12 21:39:52 UTC  
> Url: https://github.com/boostorg/math/issues/757#issuecomment-1037486246  

Ok, put a partial fix in #759. I have also added a `cubic_root_condition_number` function as all these counterexamples are of the form roots = {huge_root, small_root+eps, small_root-eps}. The condition number basically explains the error, as the new unit tests validate that the inequality:  
  
`|exact_root - computed_root|/|exact_root| <= fudge_factor*double epsilon*condition_number`  
  
hold. The `fudge_factor` is currently 10, and hence this defines how much better the routine could realistically get. However, I believe the very large root is affecting the error in ways I currently do not understand.  
  
Leaving open and the multiple root problem is still unexplained.

---

## Comment 7

> Username: SATovstun  
> Created at: 2022-02-13 11:34:44 UTC  
> Url: https://github.com/boostorg/math/issues/757#issuecomment-1038039732  

The easiest way to fix the bug with the equation `x^3 + 3*x^2 + 4*x + 2 = 0` is to replace the line  
`Real A = -boost::math::sign(R)*cbrt(abs(R) + sqrt(arg));`  
with the line  
`Real A = (R>=0?-1:1)*cbrt(abs(R) + sqrt(arg));`

---

## Comment 8

> Username: NAThompson  
> Created at: 2022-02-14 00:55:31 UTC  
> Updated at: 2022-02-14 01:57:32 UTC  
> Url: https://github.com/boostorg/math/issues/757#issuecomment-1038508135  

@SATovstun : Your expression gives the correct behavior in the limit R->0 of NR equation 5.6.13; thanks!  
  
Fixed in #760.
