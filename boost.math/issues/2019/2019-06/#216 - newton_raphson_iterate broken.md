# #216 - newton_raphson_iterate broken [Closed]

> Username: tmissir0  
> Created at: 2019-06-03 15:09:08 UTC  
> Updated at: 2019-08-11 11:44:22 UTC  
> Closed at: 2019-08-11 11:44:22 UTC  
> Url: https://github.com/boostorg/math/issues/216  

newton_raphson_iterate (math/tools/roots.hpp) appears broken since pr #145. For example, the solver returns a wrong answer for atanh(-0.98). For sure, tanh is slowly changing for arguments approaching +/-1 but the old version of the solver, such 1.67  (as well as all current first order solvers) finds the root without any problem.  
  
#include <boost/math/tools/roots.hpp>  
  
#include <iostream>  
#include <cmath>  
  
int main() {  
	std::cout << "Testing faulty 1.69 of boost::math::tools::newton_raphson_iterate test\n";  
	double target = -0.98;  
	auto target_func = [&target](double x) {  
		double f0 = std::tanh(x);  
		double f1 = 1. / std::pow(std::cosh(x), 2);  
		return std::pair<double, double>(f0 - target, f1);  
	};  
	double guess = 0.;  
	double min = -20.;  
	double max = 20.;  
	int sf = 10;  
  
	double root = boost::math::tools::newton_raphson_iterate(target_func, guess, min, max, sf);  
	double true_value = std::atanh(target);  
  
	std::cout << target << "\t" << guess << "\t" << min << "\t" << max << "\n";  
	std::cout << "Solver found atanh(" << target << ")=" << root << std::endl;  
	std::cout << "Should be atanh(" << target << ")=" << true_value << std::endl;  
	// verify value  
	std::cout << "Target function value at " << root << ": " << target_func(root).first << "\n";  
	std::cout << "Target function value at " << true_value << ": " << target_func(true_value).first << "\n";  
  
	return 0;  
}  
  
/*  
Testing faulty 1.69 of boost::math::tools::newton_raphson_iterate test  
-0.98   0       -20     20  
Solver found atanh(-0.98)=-1.91416  
Should be atanh(-0.98)=-2.29756  
Target function value at -1.91416: 0.0225668  
Target function value at -2.29756: 0  
*/

---

## Comment 1

> Username: tmissir0  
> Created at: 2019-06-03 15:45:49 UTC  
> Url: https://github.com/boostorg/math/issues/216#issuecomment-498313013  

```  
#include <boost/math/tools/roots.hpp>  
#include <iostream>  
#include <cmath>  
  
int main() {  
	std::cout << "Testing faulty 1.69 of boost::math::tools::newton_raphson_iterate test\n";  
	double target = -0.98;  
	auto target_func = [&target](double x) {  
		double f0 = std::tanh(x);  
		double f1 = 1. / std::pow(std::cosh(x), 2);  
		return std::pair<double, double>(f0 - target, f1);  
	};  
	double guess = 0.;  
	double min = -20.;  
	double max = 20.;  
	int sf = 10;  
  
	double root = boost::math::tools::newton_raphson_iterate(target_func, guess, min, max, sf);  
	double true_value = std::atanh(target);  
  
	std::cout << target << "\t" << guess << "\t" << min << "\t" << max << "\n";  
	std::cout << "Solver found atanh(" << target << ")=" << root << std::endl;  
	std::cout << "Should be atanh(" << target << ")=" << true_value << std::endl;  
	// verify value  
	std::cout << "Target function value at " << root << ": " << target_func(root).first << "\n";  
	std::cout << "Target function value at " << true_value << ": " << target_func(true_value).first << "\n";  
  
	return 0;  
}  
  
/*  
Testing faulty 1.69 of boost::math::tools::newton_raphson_iterate test  
-0.98   0       -20     20  
Solver found atanh(-0.98)=-1.91416  
Should be atanh(-0.98)=-2.29756  
Target function value at -1.91416: 0.0225668  
Target function value at -2.29756: 0  
*/  
```

---

## Comment 2

> Username: juanalday  
> Created at: 2019-06-03 16:08:51 UTC  
> Url: https://github.com/boostorg/math/issues/216#issuecomment-498322275  

The test prints 1.69, but it produces the same (faulty) results in 1.70. Checked on my end

---

## Comment 3

> Username: pabristow  
> Created at: 2019-06-04 13:20:12 UTC  
> Url: https://github.com/boostorg/math/issues/216#issuecomment-498668421  

Confirmed, and I am investigating as John Maddock is away (so you don't have the organ grinder ;-).  
  
I note immediately that converges if given a better guess like 1 or -2.  
  
#define BOOST_MATH_INSTRUMENT give some clues to its progress,  If you get to the max iterations, things have probably gone haywire.  
  
But clearly we should be able to do better.

---

## Comment 4

> Username: pabristow  
> Created at: 2019-06-04 15:46:55 UTC  
> Url: https://github.com/boostorg/math/issues/216#issuecomment-498729269  

I have yet to understand the 'fix', but I note that the current algorithm works fine for positive target from 0. to 0.99999999999  (fails for 1. but that is a special case that should be tested for and return infinity?)  
  
So, meanwhile, you could work around by considering sign of the target separately as the function is symmetrical?  
  
Still considering this ...

---

## Comment 5

> Username: pabristow  
> Created at: 2019-06-04 16:02:36 UTC  
> Url: https://github.com/boostorg/math/issues/216#issuecomment-498735794  

I also note that a much better guess, even as naive as  
  
double guess = target;  
  
improves speed of convergence.  
  
Even at target = 0.97 it works fine,   
newton-raphson_fault_216.vcxproj -> I:\Cpp\math\x64\Debug\newton-raphson_fault_216.exe  
  Autorun "I:\Cpp\math\x64\Debug\newton-raphson_fault_216.exe"  
  Testing faulty 1.69 of boost::math::tools::newton_raphson_iterate test  
  get_digits = 31  
  Newton iteration, delta = -0.503583562985739  
  Newton iteration, delta = -0.367961140868687  
  Newton iteration, delta = -0.199115991648664  
  Newton iteration, delta = -0.0491383624687034  
  Newton iteration, delta = -0.00249062610378122  
  Newton iteration, delta = -6.03592402499067e-06  
  Newton iteration, delta = -3.53392817898757e-11  
  Newton Raphson iteration, final count = 7  
  Maximum iterations: 7  
  Converged after 7 iterations.  
  0.97	0.97	-20	20  
  Solver found atanh(0.97)= 2.09229572003494  
  Should be atanh(0.97)  =  2.09229572003494  
  Target function value at 2.09229572003494: 0  
  Target function value at 2.09229572003494: 0  
  
but as you noted, the heuristics go the wrong at the 4th iteration before finally converging.  
This should allow us to do better.  Meanwhile would the original version in 1.69 work with sign separated out and a better guess, perhaps even a second term to handle the curve down?  
  
  Autorun "I:\Cpp\math\x64\Debug\newton-raphson_fault_216.exe"  
  Testing faulty 1.69 of boost::math::tools::newton_raphson_iterate test  
  get_digits = 31  
  Newton iteration, delta = -0.524228283030134  
  Newton iteration, delta = -0.41315650981895  
  Newton iteration, delta = -0.269281315424389  
  Newton iteration, delta = 10.2202393154048  
  Newton iteration, delta = 1.08276929183385  
  Newton iteration, delta = 0.109492411911825  
  Newton iteration, delta = -0.0102176756714082  
  Newton iteration, delta = -0.000103659714528818  
  Newton iteration, delta = -1.05318277176069e-08  
  Newton Raphson iteration, final count = 10  
  Maximum iterations: 10  
  Converged after 10 iterations.  
  0.98	0.98	-20	20  
  Solver found atanh(0.98)= 2.29755992506729  
  Should be atanh(0.98)  =  2.29755992506729  
  Target function value at 2.29755992506729: 0  
  Target function value at 2.29755992506729: 0

---

## Comment 6

> Username: jzmaddock  
> Created at: 2019-06-04 17:09:11 UTC  
> Url: https://github.com/boostorg/math/issues/216#issuecomment-498760322  

This line:  
  
```  
            delta = sign(delta) * result; // protect against huge jumps!  
```  
Sets delta to the wrong sign if `result` is negative, I think it should be:  
  
```  
            delta = sign(delta) * fabs(result); // protect against huge jumps!  
```  
But have no time to test....

---

## Comment 7

> Username: tmissir0  
> Created at: 2019-06-04 17:13:07 UTC  
> Url: https://github.com/boostorg/math/issues/216#issuecomment-498761745  

The tanh function is a toy example to illustrate the problem. The function on which we actually run the solver is significantly more complicated but has the same property of exponential flattening for large absolute values of the argument. Obviously, there is a limit where any numerical solver will start failing, and this limit will be reached earlier for a second-order solver. Switching to bisection, as newton_raphson_iterate does, is a very reasonable approach, and it used to work fine before this commit that aimed to resolve pr#145  
  
https://github.com/boostorg/math/commit/91c193d2d2c1913f42692795beceb5252c18c09d  
"Copies heuristics already in place for halley_iterate for the case where convergence fails."  
  
The effect of this fix does not look good, especially since halley_iterate also breaks on tanh in the same way. The best thing to do might be to revert it and re-examine pr#145.

---

## Comment 8

> Username: pabristow  
> Created at: 2019-06-05 09:02:53 UTC  
> Url: https://github.com/boostorg/math/issues/216#issuecomment-499001058  

OK - I suspected this ;-)  
  
But it does show the crucial importance of a good guess.  Newton iteration does not come with a guarantee of convergence from any starting point, it is finishing tool.  Brute force is also always likely to be *much* more expensive that a one-time computation of a good starting guess.  
  
In the atanh case, Wolfram was my friend.  https://reference.wolfram.com/language/ref/ArcTanh.html gives a series expansion where even the first term produced a dramatic improvement.  
  
But the good news is that I think that a buglet has been spotted by John (still offline-ish) and it seems to work for me for atanh example.  I will commit to the develop branch, but it may be simplest for you to patch this line of your current version to check that it works for you for atanh and more important your own function too:  
  
\boost\math\tools\roots.hpp line 277  
This line:  
            delta = sign(delta) * result; // protect against huge jumps!  
// Sets delta to the wrong sign if result is negative,   
  
I think it should be:  
            delta = sign(delta) * fabs(result); // Protect against huge jumps!  
  
and that works for me for the atanh example.  
  
Please can you try this and report back.  
  
PS You may also find that #define BOOST_MATH_INSTRUMENT  will gives you some clue about what is going on.  
  
If you start heading off in the wrong direction and so using this branch, then it suggests that you would much benefit from a better starting guess.  It is likely to lead to many wild iterations, even if you eventually get to the right answer.  In the atanh case, iterations were reduced from 11 to 3 by a naively better guess.

---

## Comment 9

> Username: pabristow  
> Created at: 2019-06-05 12:34:59 UTC  
> Updated at: 2019-06-05 12:37:16 UTC  
> Url: https://github.com/boostorg/math/issues/216#issuecomment-499064227  

commit 67dc112ba0a281f44d6a83f1ab01b74ec96913fa includes a further similar fix for the second order Halley iteration, and some cosmetic changes to enhance the output from BOOST_MATH_INSTRUMENT diagnostics.  (Caution - making this #define may produce a lot of output).  Tests pass locally.

---

## Comment 10

> Username: tmissir0  
> Created at: 2019-06-05 17:06:11 UTC  
> Url: https://github.com/boostorg/math/issues/216#issuecomment-499171308  

Satisfied with the current performance of newton_raphson_iterate. Thanks for giving it prompt attention.

---

## Comment 11

> Username: juanalday  
> Created at: 2019-06-05 17:40:10 UTC  
> Url: https://github.com/boostorg/math/issues/216#issuecomment-499183774  

Thank you so much!! I just tested and patched our internal distribution of boost with the changes.

---

## Comment 12

> Username: pabristow  
> Created at: 2019-06-06 08:53:35 UTC  
> Url: https://github.com/boostorg/math/issues/216#issuecomment-499408740  

Thank John Maddock! Should be fixed in next normal release (and is develop branch now).  
  
But can I remind you that I suspect that you hit this because of a not-very-good guess. A better guess will also speed up convergence.

---

## Comment 13

> Username: jzmaddock  
> Created at: 2019-06-06 09:50:20 UTC  
> Url: https://github.com/boostorg/math/issues/216#issuecomment-499428063  

>But can I remind you that I suspect that you hit this because of a not-very-good guess. A better guess will also speed up convergence.  
  
That's not always possible, and even when it is you can still hit pathological behaviour: for example the incomplete beta even if you're 3 or 4 digits accurate with the "guess", you may still be in a zone where the best you can do is bisect (due to pathological behaviour of Newton/Halley iteration).  
  
Meanwhile we also need to fix the tests - we have good test coverage but in the positive half of the axis, we need to replicate this in the negative half as well.... so leaving open for now.
