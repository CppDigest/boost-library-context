# #747 - boost::math::tools::cubic_roots sometimes returns incorrect roots [Closed]

> Username: SATovstun  
> Created at: 2022-01-25 20:04:44 UTC  
> Updated at: 2022-01-26 02:12:28 UTC  
> Closed at: 2022-01-26 02:12:28 UTC  
> Url: https://github.com/boostorg/math/issues/747  

When the coefficients are very large or very small, the returned roots may be absolutely wrong. Minimal working example:  
  
```  
#include <iostream>  
#include <boost/math/tools/cubic_roots.hpp>  
  
int main()  
{  
	using boost::math::tools::cubic_roots;  
	  
	std::cout << "1e-20*x^3 + x^2 - 3*x + 2 = 0:\n";  
	std::cout << "True roots (rounded): -1e20, 1, 2\n";  
	auto roots1 = cubic_roots<double>(1e-20, 1, -3, 2);  
	std::cout << "Boost roots: " << roots1[0] << ", " << roots1[1] << ", " << roots1[2] << "\n\n";  
	  
	std::cout << "x^3 - 1e20*x^2 + 3e20*x - 2e20 = 0:\n";  
	std::cout << "True roots (rounded): 1, 2, 1e20\n";  
	auto roots2 = cubic_roots<double>(1, -1e20, 3e20, -2e20);  
	std::cout << "Boost roots: " << roots2[0] << ", " << roots2[1] << ", " << roots2[2] << "\n";  
	  
	return 0;  
}  
```  
  
Output:  
```  
1e-20*x^3 + x^2 - 3*x + 2 = 0:  
True roots (rounded): -1e20, 1, 2  
Boost roots: -1e+20, -2.48353e+11, 2.48353e+11  
  
x^3 - 1e20*x^2 + 3e20*x - 2e20 = 0:  
True roots (rounded): 1, 2, 1e20  
Boost roots: -2.48353e+11, 2.48353e+11, 1e+20  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2022-01-25 22:44:45 UTC  
> Url: https://github.com/boostorg/math/issues/747#issuecomment-1021678782  

@SATovstun : Could you also print the residual and the expected residual? The interface is:  
  
```  
template<typename Real>  
std::array<Real, 2> cubic_root_residual(Real a, Real b, Real c, Real d, Real root);  
```

---

## Comment 2

> Username: SATovstun  
> Created at: 2022-01-25 22:57:31 UTC  
> Updated at: 2022-01-25 23:21:37 UTC  
> Url: https://github.com/boostorg/math/issues/747#issuecomment-1021686383  

```  
#include <iostream>  
#include <boost/math/tools/cubic_roots.hpp>  
  
int main()  
{  
	using boost::math::tools::cubic_roots;  
	using boost::math::tools::cubic_root_residual;  
	  
	std::cout << "1e-20*x^3 + x^2 - 3*x + 2 = 0:\n";  
	std::cout << "True roots (rounded): -1e20, 1, 2\n";  
	auto roots1 = cubic_roots<double>(1e-20, 1, -3, 2);  
	std::cout << "Boost roots: " << roots1[0] << ", " << roots1[1] << ", " << roots1[2] << "\n";  
	auto res10 = cubic_root_residual<double>(1e-20, 1, -3, 2, roots1[0]);  
	std::cout << "The residual (root 0) is " << res10[0] << ", and the expected residual is " << res10[1] << "\n";  
	auto res11 = cubic_root_residual<double>(1e-20, 1, -3, 2, roots1[1]);  
	std::cout << "The residual (root 1) is " << res11[0] << ", and the expected residual is " << res11[1] << "\n";  
	auto res12 = cubic_root_residual<double>(1e-20, 1, -3, 2, roots1[2]);  
	std::cout << "The residual (root 2) is " << res12[0] << ", and the expected residual is " << res12[1] << "\n\n";  
	  
	std::cout << "x^3 - 1e20*x^2 + 3e20*x - 2e20 = 0:\n";  
	std::cout << "True roots (rounded): 1, 2, 1e20\n";  
	auto roots2 = cubic_roots<double>(1, -1e20, 3e20, -2e20);  
	std::cout << "Boost roots: " << roots2[0] << ", " << roots2[1] << ", " << roots2[2] << "\n";  
	auto res20 = cubic_root_residual<double>(1, -1e20, 3e20, -2e20, roots2[0]);  
	std::cout << "The residual (root 0) is " << res20[0] << ", and the expected residual is " << res20[1] << "\n";  
	auto res21 = cubic_root_residual<double>(1, -1e20, 3e20, -2e20, roots2[1]);  
	std::cout << "The residual (root 1) is " << res21[0] << ", and the expected residual is " << res21[1] << "\n";  
	auto res22 = cubic_root_residual<double>(1, -1e20, 3e20, -2e20, roots2[2]);  
	std::cout << "The residual (root 2) is " << res22[0] << ", and the expected residual is " << res22[1] << "\n";  
	  
	return 0;  
}  
```  
  
Output:  
```  
1e-20*x^3 + x^2 - 3*x + 2 = 0:  
True roots (rounded): -1e20, 1, 2  
Boost roots: -1e+20, -2.48353e+11, 2.48353e+11  
The residual (root 0) is 5.48767e+23, and the expected residual is 2.22045e+24  
The residual (root 1) is 6.16791e+22, and the expected residual is 2.7391e+07  
The residual (root 2) is 6.16791e+22, and the expected residual is 2.7391e+07  
  
x^3 - 1e20*x^2 + 3e20*x - 2e20 = 0:  
True roots (rounded): 1, 2, 1e20  
Boost roots: -2.48353e+11, 2.48353e+11, 1e+20  
The residual (root 0) is -6.16791e+42, and the expected residual is 2.7391e+27  
The residual (root 1) is -6.16791e+42, and the expected residual is 2.7391e+27  
The residual (root 2) is 3e+40, and the expected residual is 2.22045e+44  
```

---

## Comment 3

> Username: NAThompson  
> Created at: 2022-01-26 00:10:44 UTC  
> Url: https://github.com/boostorg/math/issues/747#issuecomment-1021727529  

@SATovstun : This rootfinding problem is horribly ill-conditioned. The only solution is to use higher precision. Have you read about the Wilkinson polynomial?

---

## Comment 4

> Username: SATovstun  
> Created at: 2022-01-26 00:18:48 UTC  
> Url: https://github.com/boostorg/math/issues/747#issuecomment-1021731813  

Yes, I have read about the Wilkinson polynomial. Higher precision is a last resort. I hope to find a stable algorithm.

---

## Comment 5

> Username: NAThompson  
> Created at: 2022-01-26 00:53:40 UTC  
> Url: https://github.com/boostorg/math/issues/747#issuecomment-1021750681  

The algorithm is stable.
