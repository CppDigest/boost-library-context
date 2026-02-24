# #1133 - gauss_kronrod error since boost/1.85.0 [Closed]

> Username: Chrismarsh  
> Created at: 2024-05-09 04:29:40 UTC  
> Updated at: 2024-05-14 16:16:37 UTC  
> Closed at: 2024-05-10 23:01:59 UTC  
> Url: https://github.com/boostorg/math/issues/1133  

I have a somewhat complex code that uses a `<double>` gauss_kronrod. Since updates https://github.com/boostorg/math/commit/87a905ab1aef917d64b1c911d885966a952a30a4 and https://github.com/boostorg/math/commit/c5feae34bbdfedbe9ca5875eeb03dca8378c8a77 that were included in boost 1.85.0, the code now produces this error from within math  
  
```  
terminate called after throwing an instance of 'boost::wrapexcept<std::domain_error>'  
  what():  Error in function boost::math::expint<N5boost4math15differentiation11autodiff_v16detail4fvarIeLm1EEE>(unsigned, N5boost4math15differentiation11autodiff_v16detail4fvarIeLm1EEE): Function requires z >= 0 but got depth(1)(-3.78479410639915467471e+101,1).  
```  
  
Tracing through the code shows that on this line  
https://github.com/boostorg/math/blame/develop/include/boost/math/quadrature/gauss_kronrod.hpp#L1149  
these values are  
```  
fp	K	0.998061658175291  
kronrod_result	K	-5.41649060241513e+108  
gauss_result	K	-1.7594876201197e+106  
```  
which obviously leads to problems. The same input to this function with boost 1.84.0 produces sane values.  
  
I have a simple ish reproducer that shows that the g++ flag ` -frounding-math` is what is causing the behaviour. If I add this flag, the problem arrises. If I remove it, it goes away. This flag is needed for another library I am consuming.  
Compiler is gcc 12.3.0  
  
I'm wondering if any regression has been reported or found that could cause this type of behaviour?  
Restated, is there a way to work around this newer version being sensitive to ` -frounding-math`?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-05-09 08:21:48 UTC  
> Updated at: 2024-05-09 08:22:13 UTC  
> Url: https://github.com/boostorg/math/issues/1133#issuecomment-2102189551  

That's strange: the changes to gauss_kronrod are basically cosmetic: basically making sure we're using the correct type at all times so the code is safe to use with the new `<stdfoat>` types which have stricter conversion rules.  And -frounding-math as I understand it, makes rounding stricter/safer, so shouldn't really interfere in this case :(  
  
My guess (and it's only a guess), is that you have some numerical instability which was previously being hidden, and has been newly exposed.  A test case would be useful!

---

## Comment 2

> Username: Chrismarsh  
> Created at: 2024-05-10 04:14:59 UTC  
> Url: https://github.com/boostorg/math/issues/1133#issuecomment-2103829072  

This reproduces for me on a REHL system with gcc 12.3.0  
  
CMakeLists.txt  
```cmake  
cmake_minimum_required (VERSION 3.21)  
project(LUT CXX)  
  
find_package(Boost  
        1.85.0  
        REQUIRED)  
          
  
# only flag that matters is frounding-math, the rest are there for stepping through the trace  
set(BUILD_FLAGS "-g3 -Og -fno-inline -ggdb  -frounding-math")  
  
add_executable(  
		LUT  
		main.cpp  
)  
  
target_compile_features(LUT PRIVATE cxx_std_14)  
  
target_link_libraries(  
    LUT  
    Boost::headers  
  
)  
  
set_target_properties(LUT  
		PROPERTIES  
		COMPILE_FLAGS "${BUILD_FLAGS}"  
)  
```  
  
main.cpp  
```c++  
#include <functional>  
#include <cmath>  
#include <boost/math/special_functions/expint.hpp>  
#include <boost/math/differentiation/autodiff.hpp>  
#include <boost/math/quadrature/gauss_kronrod.hpp>  
  
using boost::math::differentiation::autodiff_fvar;  
  
template<typename TIN, typename TOUT = TIN>  
struct FunctionContainer {  
  std::function<TOUT(TIN)> standard_fun;  
  std::function<autodiff_fvar<TOUT,1>(autodiff_fvar<TIN,1>)> autodiff1_fun;  
  
  FunctionContainer() {}  
  FunctionContainer(std::function<TOUT(TIN)> fun) :  
    standard_fun(fun) {};  
  FunctionContainer(std::function<TOUT(TIN)> fun, std::function<autodiff_fvar<TOUT,1>(autodiff_fvar<TIN,1>)> fun1) :  
    standard_fun(fun), autodiff1_fun(fun1) {}  
};  
  
class TransferFunction  
{  
public:  
TransferFunction(const std::function<autodiff_fvar<double,1>(autodiff_fvar<double,1>)>& fun, double minArg, double maxArg) {  
  using boost::math::quadrature::gauss_kronrod;  
  using boost::math::differentiation::make_fvar;  
  
  /* std::function to return the first derivative of f */  
  auto f_prime = [&fun](double x) -> double {  
    return fun(make_fvar<double,1>(x)).derivative(1);  
  };  
  
  /* build the integrand. Notice that it is strictly positive */  
  auto integrand = [&f_prime](double x) -> double {  
    return static_cast<double>(1.0)/static_cast<double>(sqrt(static_cast<double>(1.0) + f_prime(x)*f_prime(x)));  
  };  
  
  double a = minArg;  
  double b = maxArg;  
  
  /* integrate over [a,b] using adaptive quadrature & default tol of sqrt(epsilon_machine). */  
  double c = gauss_kronrod<double, 15>::integrate(integrand, a, b);  
  
  /* rescale the integrand to map [a,b] -> [a,b] */  
  std::function<double(double)> g_prime = [&integrand,a,b,c](double x) -> double {  
    return (b-a) * integrand(x)/c;  
  };  
  
  /* g(a)=a and g(b)=b so  
   * g_inv_prime(a) = 1/g_prime(a) and g_inv_prime(b) = 1/g_prime(b) */  
  double m0 = 1/g_prime(a);  
  double m1 = 1/g_prime(b);  
  
  std::cout << "m0 = " << m0 << " m1 = " << m1 << std::endl;  
}  
};  
  
  
template <typename T>  
T TPSBasis(T x){  
  T gamma = 0.5772156649015328606;  
  return (x <= 32) ? -(log(x) + gamma + boost::math::expint(1,x)) : -(log(x) + gamma);  
}  
  
  
int main(){  
  TransferFunction T(TPSBasis<autodiff_fvar<double,1>>, 1e-5, 32);  
}  
```

---

## Comment 3

> Username: NAThompson  
> Created at: 2024-05-10 10:59:45 UTC  
> Updated at: 2024-05-10 14:50:10 UTC  
> Url: https://github.com/boostorg/math/issues/1133#issuecomment-2104406609  

I made a couple diffs to diagnose:  
  
```cpp  
#include <functional>  
#include <cmath>  
#include <boost/math/special_functions/expint.hpp>  
#include <boost/math/differentiation/autodiff.hpp>  
#include <boost/math/quadrature/gauss_kronrod.hpp>  
  
using boost::math::differentiation::autodiff_fvar;  
  
template<typename TIN, typename TOUT = TIN>  
struct FunctionContainer {  
  std::function<TOUT(TIN)> standard_fun;  
  std::function<autodiff_fvar<TOUT,1>(autodiff_fvar<TIN,1>)> autodiff1_fun;  
  
  FunctionContainer() {}  
  FunctionContainer(std::function<TOUT(TIN)> fun) :  
    standard_fun(fun) {};  
  FunctionContainer(std::function<TOUT(TIN)> fun, std::function<autodiff_fvar<TOUT,1>(autodiff_fvar<TIN,1>)> fun1) :  
    standard_fun(fun), autodiff1_fun(fun1) {}  
};  
  
class TransferFunction  
{  
public:  
TransferFunction(const std::function<autodiff_fvar<double,1>(autodiff_fvar<double,1>)>& fun, double minArg, double maxArg) {  
  using boost::math::quadrature::gauss_kronrod;  
  using boost::math::differentiation::make_fvar;  
  
  /* std::function to return the first derivative of f */  
  auto f_prime = [&fun](double x) -> double {  
    return fun(make_fvar<double,1>(x)).derivative(1);  
  };  
  
  /* build the integrand. Notice that it is strictly positive */  
  auto integrand = [&f_prime](double x) -> double {  
    return static_cast<double>(1.0)/static_cast<double>(sqrt(static_cast<double>(1.0) + f_prime(x)*f_prime(x)));  
  };  
  
  double a = minArg;  
  double b = maxArg;  
  
  /* integrate over [a,b] using adaptive quadrature & default tol of sqrt(epsilon_machine). */  
  double L1 = 0;  
  double error = 0;  
  double c = gauss_kronrod<double, 15>::integrate(integrand, a, b, 15, std::sqrt(std::numeric_limits<double>::epsilon()), &error, &L1);  
  std::cout << "Error estimate = " << error << "\n";  
  std::cout << "L1 norm = " << L1 << "\n";   
  std::cout << "c = " << c << "\n";  
  
  /* rescale the integrand to map [a,b] -> [a,b] */  
  std::function<double(double)> g_prime = [&integrand,a,b,c](double x) -> double {  
    return (b-a) * integrand(x)/c;  
  };  
  
  /* g(a)=a and g(b)=b so  
   * g_inv_prime(a) = 1/g_prime(a) and g_inv_prime(b) = 1/g_prime(b) */  
  double m0 = 1/g_prime(a);  
  double m1 = 1/g_prime(b);  
  
  std::cout << "m0 = " << m0 << " m1 = " << m1 << std::endl;  
}  
};  
  
  
template <typename T>  
T TPSBasis(T x){  
  T gamma = 0.5772156649015328606;  
  return (x <= 32) ? log(x) + gamma + boost::math::expint(1,x) : -(log(x) + gamma);  
}  
  
  
int main(){  
  TransferFunction T(TPSBasis<autodiff_fvar<double,1>>, 1e-5, 32);  
}  
  
```  
  
But with `clang` everything seems good:  
  
```  
reproduce $ make  
/opt/homebrew/opt/llvm/bin/clang++ -g3 -Og -fno-inline -ggdb  -frounding-math --std=c++14 -I./include -I/opt/homebrew/opt/llvm/include  -I/opt/homebrew/opt/boost@1.85/include main.cpp  
reproduce $ ./a.out  
Error estimate = 2.05468e-09  
L1 norm = 31.4626  
c = 31.4626  
m0 = 1.39046 m1 = 0.983687  
```  
  
I also tried gcc-12 and gcc-14 on ARM, still good. So it looks like this one might be miscompilation for the x86 target?  
  
@mborland , @jzmaddock : Do y'all have an x86 machine with gcc-12?

---

## Comment 4

> Username: Chrismarsh  
> Created at: 2024-05-10 20:00:03 UTC  
> Url: https://github.com/boostorg/math/issues/1133#issuecomment-2105196164  

I haven't had a chance to try on arm64, but a colleague did and agrees it doesn't reproduce with clang or brew gcc13.  
I also tried clang10 on the x86_64 machine I can reproduce on, and clang does not reproduce the error.  
  
Poking around, I found this gcc bug  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=109359  
which looks suspiciously similar to the behaviour I am seeing.  At the end it is noted  
  
```  
Fixed for 12.4/13.3/14.0.  
```  
I built gcc 13.2 and gcc 14.0 with spack.  
  
gcc 13.2 reproduces the error  
gcc 14.0 **does not** reproduce the error.  
  
Which, if this is due to the gcc regression, would be expected.   
  
Compiler explorer only has up to boost 1.84.0, and it does not reproduce there (as per my experience before I updated to boost 1.85.0).  
https://godbolt.org/z/cE3Ezqhr5  
  
Do you think/agree it is this compiler regression that is causing the issue?

---

## Comment 5

> Username: NAThompson  
> Created at: 2024-05-10 21:25:15 UTC  
> Url: https://github.com/boostorg/math/issues/1133#issuecomment-2105289833  

> Do you think/agree it is this compiler regression that is causing the issue?  
  
Could you see if removing `-frounding-math` fixes it? That would basically resolve it in my mind.

---

## Comment 6

> Username: Chrismarsh  
> Created at: 2024-05-10 21:57:36 UTC  
> Url: https://github.com/boostorg/math/issues/1133#issuecomment-2105318023  

Yes, as I noted above, I only hit this problem when `-frounding-math` is specified. I use CGAL which requires `-frounding-math`, so I cannot remove it.  
  
At this point, I'm pretty convinced this is a bug in g++ that is now resolved. I'm curious if you agree.  
  
If the problem is the compiler, then that's a different problem and requires a different solution.

---

## Comment 7

> Username: NAThompson  
> Created at: 2024-05-10 23:01:59 UTC  
> Url: https://github.com/boostorg/math/issues/1133#issuecomment-2105364356  

>  I'm curious if you agree.  
  
I agree. Closing; but feel free to reopen if new information arises.

---

## Comment 8

> Username: Chrismarsh  
> Created at: 2024-05-14 16:16:36 UTC  
> Url: https://github.com/boostorg/math/issues/1133#issuecomment-2110630200  

I have further reproduced with a simpler example using one of the constants in `gauss_kronrod.hpp`  
  
```  
#include <iostream>  
#include <array>  
  
static constexpr std::array<double, 1> datad = {  
         static_cast<double>(5.86087235467691130294144838258729598e-01L)};  
static constexpr std::array<long double, 1> datal = {  
         static_cast<long double>(5.86087235467691130294144838258729598e-01L)};  
static constexpr std::array<double, 1> datad2 = {  
    5.860872355e-01f};  
static constexpr std::array<float, 1> dataf = {  
    5.860872355e-01f};  
  
  
int main() {  
  std::cout << datal[0]  << std::endl;  
  std::cout << datad[0]  << std::endl;  
  std::cout << datad2[0] << std::endl;  
  std::cout << dataf[0]  << std::endl;  
  
}  
  
```  
```  
Program stdout  
  
0.586087  
-1.64662e-202  
0.586087  
0.586087  
```  
  
Looking at the asm confirms it is the same weird negative long values as reported in that bug.   
  
https://godbolt.org/z/r6zfEh1b4
