# #155 - Assert failed in tanh_sinh for multiprecision cpp_bin_float and cpp_dec_float [Closed]

> Username: dfche  
> Created at: 2018-11-14 02:33:13 UTC  
> Updated at: 2018-11-14 20:07:50 UTC  
> Closed at: 2018-11-14 20:07:50 UTC  
> Url: https://github.com/boostorg/math/issues/155  

This may be closely related to #108   
  
It involves quadrature of a simple constant over range [0,1] using tanh_sinh from boost_1_68_0  
  
Assertion `(left_min_complement * diff + a) > a' failed.  
  
---------------------------------------  
The code for file problem.cpp  
  
#include <boost/math/quadrature/tanh_sinh.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp> // For cpp_bin_float_50.  
#include <boost/multiprecision/cpp_dec_float.hpp> // For cpp_dec_float_50.  
#include <iostream>  
  
  
template <class T>  
void integration(T b)  
{  
  using namespace boost::multiprecision;  
    
  std::cout << "Quadrature  " << std::endl;  
  std::cout << std::numeric_limits<T>::digits << std::endl;  
  std::cout << std::numeric_limits<T>::digits10 << std::endl;  
  std::cout.precision(std::numeric_limits<T>::max_digits10);  
  std::cout << b << std::endl;  
  std::cout << log(b) << std::endl;  
    
  // Show all possibly significant decimal digits  
  // std::cout.precision(std::numeric_limits<T>::digits10);  
  // Show all guaranteed significant decimal digits for T  
  
  boost::math::quadrature::tanh_sinh<T> integrator;  
  
  auto f = [](T x) { return 7; };  
  T termination = sqrt(std::numeric_limits<T>::epsilon());  
  T error;  
  T L1;  
  size_t levels;  
  T xlo = 0;  
  T xhi = 1;  
  T Q;  
    
  try {  
    Q = integrator.integrate(f,xlo,xhi,termination,&error,&L1,&levels);  
  }  
  catch (const std::exception& e)    
  {  
    std::cout <<  
      "\n""Message from thrown exception was:\n   " << e.what() << std::endl;  
  }  
  std::cout << "Q " << Q << " " << std::endl;  
  std::cout << "error " << error << " " << std::endl;  
  std::cout << "L1 " << L1 << " " << std::endl;  
  std::cout << "levels " << levels << " " << std::endl;  
  
  T condition_number = L1/abs(Q);  
    
  std::cout << "condition_number " << condition_number << " " << std::endl;    
    
}  
  
int main()  
{  
  boost::multiprecision::cpp_bin_float_50 two_bin50 = 2;  
  integration(two_bin50);  
  boost::multiprecision::cpp_dec_float_50 two_50 = 2;  
  integration(two_50);  
}  
---------------------------------------  
The makefile  
  
problem:	problem.o  
	g++ problem.o -lquadmath -o problem  
  
problem.o: problem.cpp  
	g++ -c -g -Wall -std=gnu++11 -I/home/dfc/boost_1_68_0 problem.cpp  
  
---------------------------------------  
The result of ./problem  
  
Quadrature    
168  
50  
2  
0.69314718055994530941723212145817656807550013436025633  
problem: /home/dfc/boost_1_68_0/boost/math/quadrature/tanh_sinh.hpp:170: decltype ((Real)(declval<F>()(declval<Real>()))) boost::math::quadrature::tanh_sinh<Real, Policy>::integrate(F, Real, Real, Real, Real*, Real*, std::size_t*) [with F = integration(T) [with T = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >]::<lambda(boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >)>; Real = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >; Policy = boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>; decltype ((Real)(declval<F>()(declval<Real>()))) = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >; std::size_t = long unsigned int]: Assertion `(left_min_complement * diff + a) > a' failed.  
Aborted (core dumped)

---

## Comment 1

> Username: dfche  
> Created at: 2018-11-14 02:39:07 UTC  
> Url: https://github.com/boostorg/math/issues/155#issuecomment-438516550  

I see that some of the copy/pasted text was omitted. Here is the source file  
[problem.cpp.txt](https://github.com/boostorg/math/files/2578990/problem.cpp.txt)

---

## Comment 2

> Username: NAThompson  
> Created at: 2018-11-14 04:39:30 UTC  
> Url: https://github.com/boostorg/math/issues/155#issuecomment-438535046  

Took a look at this; it also fails in `cpp_bin_float_100` precision. I added the following to engage in some old fashioned printf debugging in `tanh_sinh.hpp`, near line 170:  
  
```cpp  
          //  
          // These asserts will fail only if rounding errors on  
          // type Real have accumulated so much error that it's  
          // broken our internal logic.  Should that prove to be  
          // a persistent issue, we might need to add a bit of fudge  
          // factor to move left_min_complement and right_min_complement  
          // further from the end points of the range.  
          //  
          std::cout << std::setprecision(std::numeric_limits<Real>::digits10 + 5);  
          std::cout << "a = " << a << ", diff = " << diff << ", left min complement = " << left_min_complement << "\n";  
          std::cout << "left_min_complement*diff = " << left_min_complement*diff << "\n";  
          std::cout << "left_min_complement*diff + a = " << left_min_complement*diff + a << "\n";  
          //BOOST_ASSERT((left_min_complement * diff + a) > a);  
```  
  
Up to and including float128 precision, `left_min_complement` is small, but when multiplied by `diff`, it's still strictly greater than zero. For higher precision types, `left_min_complement * diff` is identically zero:  
  
```  
Double precision:  
a = 0, diff = 0.5, left min complement = 2.2250738585072013831e-308  
left_min_complement*diff = 1.1125369292536006915e-308  
left_min_complement*diff + a = 1.1125369292536006915e-308  
Q = 7, error estimate 5.1159076974727213383e-13  
Long double precision:  
a = 0, diff = 0.5, left min complement = 3.3621031431120935062627e-4932  
left_min_complement*diff = 1.6810515715560467531313e-4932  
left_min_complement*diff + a = 1.6810515715560467531313e-4932  
Q = 6.9999999999999999995663, error estimate 5.1217970836736625983576e-13  
f128 precision:  
a = 0, diff = 0.5, left min complement = 3.3621031431120935062626778173217526026e-4932  
left_min_complement*diff = 1.6810515715560467531313389086608763013e-4932  
left_min_complement*diff + a = 1.6810515715560467531313389086608763013e-4932  
Q = 7.0000000000000000000000000000000007704, error estimate 1.4657097248764619736097214534248330865e-29  
cpp_bin_float_50:  
a = 0, diff = 0.5, left min complement = 7.946356111082342307457893879439704967012146530020480575e-646456893  
left_min_complement*diff = 0  
left_min_complement*diff + a = 0  
Q = 6.999999999999999999999999999999999999999999999999978618, error estimate 1.465761109124427792862956000881513723394755627486166535e-29  
cpp_bin_float_100:  
a = 0, diff = 0.5, left min complement = 6.952269265339507690356354763502704633697604806656431858540542038833690440404672281073174878591251772396e-646456793  
left_min_complement*diff = 0  
left_min_complement*diff + a = 0  
Q = 6.99999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999954281, error estimate 2.98475148848006375073427203825995221068092517851577419923415533040487417332354175553385691994398041350653e-63  
cpp_dec_float_100:  
a = 0, diff = 0.5, left min complement = 1e-67108864  
left_min_complement*diff = 0  
left_min_complement*diff + a = 0  
Q = 6.999999999999999999999999999999999999999999999999937407, error estimate 1.4657611091244277928683711273881664810262235e-29  
```  
  
I have confirmed that in all cases, the condition  
  
```cpp  
if (left_min_complement < tools::min_value<Real>())  
```  
(`tanh_sinh.hpp` ~line 160) is satisfied, so in each case, `left_min_complement` is set to `tools::min_value<Real>()`. It appears that the multiprecision numbers don't denorm the same way double, long double, and float128 do, so I made the following change:  
  
  
```cpp  
using std::max;  
if (left_min_complement < max(tools::min_value<Real>(), tools::min_value<Real>()/diff) ) {  
               left_min_complement = max(tools::min_value<Real>(), tools::min_value<Real>()/diff);  
}  
```  
  
The assertions are not hit with this change, tests still pass. I'll let John sign off on the fix because it's not clear to me that this change makes the code infallible.

---

## Comment 3

> Username: pabristow  
> Created at: 2018-11-14 11:49:53 UTC  
> Url: https://github.com/boostorg/math/issues/155#issuecomment-438635866  

"Die ganzen Zahlen hat der liebe Gott gemacht, alles andere ist Menschenwerk."  
Integers are made by the Good Lord, all others are man's work.  
 -- Leopold Kronecker  
  
and der liebe Gott  would definitely not bless denormalized numbers!

---

## Comment 4

> Username: dfche  
> Created at: 2018-11-14 16:39:19 UTC  
> Url: https://github.com/boostorg/math/issues/155#issuecomment-438729591  

Thanks, I confirm this works. Looking at the code, should the same fix be applied to the lines that follow that refer to right_min_complement?  
  
 Real right_min_complement = avg_over_diff_p1 - float_prior(avg_over_diff_p1);  
          if (right_min_complement < tools::min_value<Real>())  
             right_min_complement = tools::min_value<Real>();

---

## Comment 5

> Username: NAThompson  
> Created at: 2018-11-14 18:29:37 UTC  
> Url: https://github.com/boostorg/math/issues/155#issuecomment-438768133  

@dfche : Nice catch,  yes, I managed to hit that assertion by changing the domain of integration from [0,1] to [-1, 0]. For reference, here's the fix:  
  
```cpp  
using std::max;  
Real smallest_complement = max(tools::min_value<Real>(), tools::min_value<Real>()/diff);  
if (left_min_complement < smallest_complement ) {  
        left_min_complement = smallest_complement;  
}  
Real right_min_complement = avg_over_diff_p1 - float_prior(avg_over_diff_p1);  
if (right_min_complement < smallest_complement) {  
    right_min_complement = smallest_complement;  
}  
```  
  
I also tested some huge intervals, because perhaps if `diff` is 10^12 then this will take the branch just because `smallest_complement` comes out large. But I wasn't able to generate any assertion fails nor discover any loss of accuracy. Got to keep muddling along . . .

---

## Comment 6

> Username: jzmaddock  
> Created at: 2018-11-14 19:02:33 UTC  
> Url: https://github.com/boostorg/math/issues/155#issuecomment-438779401  

I've pushed a fix to develop for this that mirrors Nicks code (looks like we were working on this at the same time).  
  
>I also tested some huge intervals, because perhaps if diff is 10^12 then this will take the branch just because smallest_complement comes out large  
  
No if the interval is large then your `smallest_complement` will be `tools::min_value()` as before.  If the interval is micro-small though then `smallest_complement ` may come out larger, but unless someone tries to integrate over [0, std::numeric_limits<T>::min()] I don't think this can ever be an issue?  
  
One open issue: this wasn't discovered by our tests because we run them in release mode as they're just too slow otherwise.  They do trigger reproduce the original issue run in debug mode from my IDE though.  It would be good to find a way to trigger the asserts while keeping compiler optimisations on, but we have to be careful as we're already getting too many timeouts during CI :(
