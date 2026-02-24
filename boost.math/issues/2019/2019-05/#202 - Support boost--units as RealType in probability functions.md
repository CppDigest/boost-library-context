# #202 - Support boost::units as RealType in probability functions [Open]

> Username: sfo  
> Created at: 2019-05-07 08:53:22 UTC  
> Updated at: 2019-05-07 17:37:02 UTC  
> Url: https://github.com/boostorg/math/issues/202  

This one may be a meta-issue collecting several dependent issues that have to be solved to provide the new feature. I discovered those when trying to find a quick fix for my problem. If requested, I can create a github issue for each of it.  
  
Currently, probability functions are implemented using templates with parameter `RealType`. However, the way some features are implemented break the use of unit types from the `boost::units` package.  
  
1. The validation of parameter values does not account for implementation of, e.g. `operator<=` of `RealType`. The code of the third example below gives the following error:  
  
> error: no match for ‘operator<=’ (operand types are ‘boost::units:: [...]’ and ‘int’)  
  
This error appears for the following code at line [72 of boost/math/distributions/detail/common_error_handling.hpp](https://github.com/boostorg/math/blob/c759981a2af05c7319c07e532e070e59a72cc74f/include/boost/math/distributions/detail/common_error_handling.hpp#L72), where comparison between `boost::units::quantity<boost::units::si::length>` and `int` fails:  
```C++  
72    if((scale <= 0) || !(boost::math::isfinite)(scale))  
```     
  
2. boost::math::cdf and boost::math::pdf return `RealType`: The PDF and CDF function should return a dimensionless probability value (i.e. double between 0 and 1) and not a value of `RealType`:  
```C++  
typedef boost::units::quantity<boost::units::si::length> length;  
#define meter boost::units::si::meter  
  
boost::math::normal_distribution<length> meterDist(0 * meter, 1 * meter);  
length prob = boost::math::cdf(meterDist, 0 * meter); // should return 0.5, not 0.5 meters  
```  
  
3. The implementation of PDF calculation does not respect units. Here, the resulting units of the calculations in [line 145 and 146](https://github.com/boostorg/math/blob/c759981a2af05c7319c07e532e070e59a72cc74f/include/boost/math/distributions/normal.hpp#L144) do not match the unit of the receiving type:  
```C++  
// lines 144 - 146 of boost/math/distributions/normal.hpp  
144    RealType exponent = x - mean; // [m] = [m] - [m]  
145    exponent *= -exponent;        // [m] = [m] * [m]                                                                                                                                                                                   
146    exponent /= 2 * sd * sd;      // [m] = [m]*[m] / [m]*[m]  
```  
  
4. Weibull distribution uses the same type for each of the two parameters, shape and scale. However, when looking at [the definition of the distribution](https://en.wikipedia.org/wiki/Weibull_distribution#Standard_parameterization), it becomes clear that lambda should be of `RealType` (e.g. meter, second, etc.) while k has to be dimensionless.  
  
Note: The examples do not yet compile without changes to several boost header files.

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-05-07 13:07:28 UTC  
> Url: https://github.com/boostorg/math/issues/202#issuecomment-490071566  

Actually all `int` comparisons are a cause of pain, since ideally we'd like the library to work with floats, multiprecision, autodiff variables, interval types, and now unit types.

---

## Comment 2

> Username: pabristow  
> Created at: 2019-05-07 13:59:16 UTC  
> Url: https://github.com/boostorg/math/issues/202#issuecomment-490091755  

Boost.Math predates Boost.Units by many years.  Over a decade ago, getting all the functions to work with template RealType rather than plain double was a big step forward and later made use of Boost.Multiprecision possible, and then other types mentioned.  
  
error-handling Line 72 might be 'fixed' by static casting zero to RealType?  We have dealt with many cases of this elsewhere.  Is this one we have missed?  If you have a test case, you might like to try this and report?  
  
CDF and PDF probability are, of course, between 0. and 1. but these might be much more precise than double if RealType is a multiprecision type like cpp_bin_float_50.   Does being dimensionless imply that the result type is different from RealType?  
  
If you can share your test cases, this will help us try to improve compatibility.  
  
What you are trying to do sounds reasonable and desirable, but probably more than a meta-issue, more a mega-issue!

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-05-07 14:20:07 UTC  
> Url: https://github.com/boostorg/math/issues/202#issuecomment-490100207  

Not so fast guys - the comparison to a literal zero is a red-herring here, and genuinely what we want to do anyway as:  
  
`x >= 0`  
  
is orders of magnitude more efficient than:  
  
`x >= Real(0)`  
  
when dealing with multiprecision types.  
  
The decision to compare to integer literals was quite deliberate.  
  
I also doubt very much that the internals of any of the distributions can be made "unit safe", as a trivial example the cdf of the normal distribution requires calculating erf on the supplied dimension (at least I think so, what dimension if any is a variance???).  
  
*If* we decided to support this, I think it would have to be by partially specialising the distribution classes and forwarding (internally) to a dimensionless implementation.

---

## Comment 4

> Username: NAThompson  
> Created at: 2019-05-07 14:32:44 UTC  
> Updated at: 2019-05-07 14:38:39 UTC  
> Url: https://github.com/boostorg/math/issues/202#issuecomment-490105566  

Can you compare multiprecisions against any int quickly? Or just zero?  
  
I note that I have really great antipathy towards units libraries, because we've had the Buckingham Pi theorem for a hundred years. But nobody cares about that-although I had thought nobody cared about units libraries either!  
  
I was under the impression that we'd have no additional work to do to support units other than make sure that the relevant comparison operators were defined--the units library should detect if a dimensional quantity is passed into a transcendental function and fail at compile time. Is this not correct?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2019-05-07 17:34:53 UTC  
> Url: https://github.com/boostorg/math/issues/202#issuecomment-490176494  

zero is often handled as a special case yes (though not as thoroughly as I'd hoped, needs investigating more).  However, for types like mpfr_float, all comparisons to int go through `mpfr_cmp_si` which is about 10x quicker than constructing and comparing to another mpfr_float.  
  
Using:  
  
```  
template <class Real>  
void BM_equal_int(benchmark::State& state) {  
  std::mt19937 gen(323723);  
  std::uniform_real_distribution<double> dist(-50.95, 50.95);  
  
  Real data[1000];  
  bool results[1000];  
  
  for (unsigned i = 0; i < 1000; ++i) data[i] = dist(gen);  
  
  for (auto _ : state) {  
    for (unsigned i = 0; i < 1000; ++i) results[i] = (data[i] == 0);  
  }  
}  
BENCHMARK_TEMPLATE(BM_equal_int, float);  
BENCHMARK_TEMPLATE(BM_equal_int, double);  
BENCHMARK_TEMPLATE(BM_equal_int, boost::multiprecision::mpfr_float);  
  
template <class Real>  
void BM_equal_real(benchmark::State& state) {  
  std::mt19937 gen(323723);  
  std::uniform_real_distribution<double> dist(-50.95, 50.95);  
  
  Real data[1000];  
  bool results[1000];  
  
  for (unsigned i = 0; i < 1000; ++i) data[i] = dist(gen);  
  
  for (auto _ : state) {  
    for (unsigned i = 0; i < 1000; ++i) results[i] = (data[i] == static_cast<Real>(0));  
  }  
}  
  
BENCHMARK_TEMPLATE(BM_equal_real, float);  
BENCHMARK_TEMPLATE(BM_equal_real, double);  
BENCHMARK_TEMPLATE(BM_equal_real, boost::multiprecision::mpfr_float);  
```  
  
I see:  
  
```  
Benchmark                                                     Time             CPU   Iterations  
-----------------------------------------------------------------------------------------------  
BM_equal_int<float>                                        1022 ns         1004 ns       746667  
BM_equal_int<double>                                        474 ns          465 ns      1445161  
BM_equal_int<boost::multiprecision::mpfr_float>            8982 ns         8998 ns        74667  
BM_equal_real<float>                                        390 ns          392 ns      1792000  
BM_equal_real<double>                                       426 ns          424 ns      1659259  
BM_equal_real<boost::multiprecision::mpfr_float>         117580 ns       119275 ns         4978  
```  
  
The first float case is an outlier BTW.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2019-05-07 17:37:02 UTC  
> Url: https://github.com/boostorg/math/issues/202#issuecomment-490177307  

Changing the code to measure > 2 time I see the same results:  
  
```  
Benchmark                                                     Time             CPU   Iterations  
-----------------------------------------------------------------------------------------------  
BM_equal_int<float>                                         120 ns          119 ns      7466667  
BM_equal_int<double>                                        117 ns          114 ns      5600000  
BM_equal_int<boost::multiprecision::mpfr_float>            8831 ns         8789 ns        74667  
BM_equal_real<float>                                       86.1 ns         85.4 ns      8960000  
BM_equal_real<double>                                      77.8 ns         76.7 ns      8960000  
BM_equal_real<boost::multiprecision::mpfr_float>          89431 ns        90332 ns         6400  
```
