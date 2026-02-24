# #40 - Error using odeint with float128 [Closed]

> Username: cffk  
> Created at: 2019-10-01 21:46:00 UTC  
> Updated at: 2023-12-18 09:35:31 UTC  
> Closed at: 2023-12-18 09:35:31 UTC  
> Url: https://github.com/boostorg/odeint/issues/40  

There's a compile time error using odeint with the float128 introduced  
somewhere between boost 1.66 and boost 1.69 (or possible between g++  
8.3.1 and g++ 9.2.1).  
  
The following copy compiles and runs successfully (integrating a  
simple harmonic oscillator) with PRECISION set to 1, 2, 3, and 4 (for  
float, double, long double, and float128) on a Fedora Linux 29 system  
with  
  
    boost 1.66  
    g++ 8.3.1  
  
However after upgrading to Fedora Linux 30, i.e.,  
  
    boost 1.69  
    g++ 9.2.1  
  
the float128 precision version fails to compile.  Here is the test code:  
  
    // compile with  
    //   g++ -O3            -DPRECISION=1 -o bug1 bug.cpp  
    //   g++ -O3            -DPRECISION=2 -o bug2 bug.cpp  
    //   g++ -O3            -DPRECISION=3 -o bug3 bug.cpp  
    //   g++ -O3 -lquadmath -DPRECISION=4 -o bug4 bug.cpp  
  
    #include <iostream>  
    #include <iomanip>  
    #include <vector>  
    #include <array>  
    #include <boost/multiprecision/float128.hpp>  
    #include <boost/numeric/odeint.hpp>  
  
    #if !defined(PRECISION)  
    #define PRECISION 4  
    #endif  
  
    namespace ode = boost::numeric::odeint;  
    #if PRECISION == 1  
    typedef float real;  
    #elif PRECISION == 2  
    typedef double real;  
    #elif PRECISION == 3  
    typedef long double real;  
    #elif PRECISION == 4  
    typedef boost::multiprecision::float128 real;  
    #else  
    typedef double real  
    #endif  
  
    typedef std::array<real, 2> point;  
  
    class Force {  
    public:  
      Force() {}  
      void operator() (const point& p, point &d, const real /*t*/) const {  
	d[0] = p[1];  
	d[1] = -p[0];  
      }  
    };  
  
    class ParticleFollow {  
    public:  
      static void follow(Force& sys, const point& p0, real t0, real ds,  
			 real tmax, std::vector<point>& points) {  
	ode::result_of::make_dense_output  
	  < ode::runge_kutta_dopri5< point, real > >::type integrator =  
	  ode::make_dense_output(real(1.0e-8), real(0.01*1.0e-8),  
				 ode::runge_kutta_dopri5< point, real >() );  
	integrator.initialize(p0, t0, real(1e-2));  
	integrator.do_step(sys);  
	int n = 1, i = 1;  
	point out;  
	while (true) {  
	  real tout = i * ds;  
	  while (integrator.current_time() < tout) {  
	    integrator.do_step(sys);  
	    ++n;  
	  }  
	  integrator.calc_state(tout, out);  
	  points.push_back(out);  
	  ++i;  
	  if (tout > tmax) break;  
	}  
      }  
    };  
  
    int main() {  
      std::vector<point> result;  
      point p0;  
      p0[0] = real(1); p0[1] = real(0);  
      Force sys;  
      ParticleFollow::follow(sys, p0, 0, 0.1, 6.29, result);  
      std::cout << std::fixed << std::setprecision(4);  
      for (size_t i = 0; i < result.size(); ++i)  
	std::cout << result[i][0] << " " << result[i][1] << "\n";  
    }  
  
and here is the output from the compiler when PRECISION=4:  
  
    In file included from /usr/include/boost/numeric/odeint/algebra/norm_result_type.hpp:20,  
		     from /usr/include/boost/numeric/odeint/algebra/range_algebra.hpp:28,  
		     from /usr/include/boost/numeric/odeint/stepper/euler.hpp:25,  
		     from /usr/include/boost/numeric/odeint.hpp:27,  
		     from bug.cpp:12:  
    /usr/include/boost/numeric/odeint/algebra/detail/extract_value_type.hpp: In instantiation of ‘struct boost::numeric::odeint::detail::extract_value_type<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, void>’:  
    /usr/include/boost/numeric/odeint/algebra/detail/extract_value_type.hpp:47:73:   required from ‘struct boost::numeric::odeint::detail::extract_value_type<std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>, void>’  
    /usr/include/boost/numeric/odeint/algebra/norm_result_type.hpp:28:60:   required from ‘struct boost::numeric::odeint::norm_result_type<std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>, void>’  
    /usr/include/boost/numeric/odeint/algebra/array_algebra.hpp:276:64:   required by substitution of ‘template<template<class, long unsigned int <anonymous> > class Array, class T, long unsigned int dim> static typename boost::numeric::odeint::norm_result_type<Array<T, dim> >::type boost::numeric::odeint::array_algebra::norm_inf(const Array<T, dim>&) [with Array = std::array; T = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; long unsigned int dim = 2]’  
    /usr/include/boost/numeric/odeint/stepper/controlled_runge_kutta.hpp:89:40:   required from ‘boost::numeric::odeint::default_error_checker<Value, Algebra, Operations>::value_type boost::numeric::odeint::default_error_checker<Value, Algebra, Operations>::error(boost::numeric::odeint::default_error_checker<Value, Algebra, Operations>::algebra_type&, const State&, const Deriv&, Err&, Time) const [with State = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>; Deriv = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>; Err = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>; Time = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Value = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Algebra = boost::numeric::odeint::array_algebra; Operations = boost::numeric::odeint::default_operations; boost::numeric::odeint::default_error_checker<Value, Algebra, Operations>::value_type = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; boost::numeric::odeint::default_error_checker<Value, Algebra, Operations>::algebra_type = boost::numeric::odeint::array_algebra]’  
    /usr/include/boost/numeric/odeint/stepper/controlled_runge_kutta.hpp:768:20:   required from ‘boost::numeric::odeint::controlled_step_result boost::numeric::odeint::controlled_runge_kutta<ErrorStepper, ErrorChecker, StepAdjuster, Resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>::try_step(System, const StateIn&, const DerivIn&, boost::numeric::odeint::controlled_runge_kutta<ErrorStepper, ErrorChecker, StepAdjuster, Resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>::time_type&, StateOut&, DerivOut&, boost::numeric::odeint::controlled_runge_kutta<ErrorStepper, ErrorChecker, StepAdjuster, Resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>::time_type&) [with System = Force; StateIn = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>; DerivIn = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>; StateOut = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>; DerivOut = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>; ErrorStepper = boost::numeric::odeint::runge_kutta_dopri5<std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >; ErrorChecker = boost::numeric::odeint::default_error_checker<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations>; StepAdjuster = boost::numeric::odeint::default_step_adjuster<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >; Resizer = boost::numeric::odeint::initially_resizer; boost::numeric::odeint::controlled_runge_kutta<ErrorStepper, ErrorChecker, StepAdjuster, Resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>::time_type = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>]’  
    /usr/include/boost/numeric/odeint/stepper/dense_output_runge_kutta.hpp:338:17:   required from ‘std::pair<typename Stepper::stepper_type::time_type, typename Stepper::stepper_type::time_type> boost::numeric::odeint::dense_output_runge_kutta<Stepper, boost::numeric::odeint::explicit_controlled_stepper_fsal_tag>::do_step(System) [with System = Force; Stepper = boost::numeric::odeint::controlled_runge_kutta<boost::numeric::odeint::runge_kutta_dopri5<std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >, boost::numeric::odeint::default_error_checker<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations>, boost::numeric::odeint::default_step_adjuster<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >, boost::numeric::odeint::initially_resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>; typename Stepper::stepper_type::time_type = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>]’  
    bug.cpp:51:27:   required from here  
    /usr/include/boost/numeric/odeint/algebra/detail/extract_value_type.hpp:47:73: error: invalid use of incomplete type ‘struct boost::numeric::odeint::detail::extract_value_type<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, void>’  
       47 |     typedef typename extract_value_type< typename S::value_type >::type type;  
	  |                                                                         ^~~~  
    /usr/include/boost/numeric/odeint/algebra/detail/extract_value_type.hpp:44:8: note: definition of ‘struct boost::numeric::odeint::detail::extract_value_type<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, void>’ is not complete until the closing brace  
       44 | struct extract_value_type< S , typename boost::enable_if< has_value_type<S> >::type >  
	  |        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
    In file included from /usr/include/boost/numeric/odeint.hpp:35,  
		     from bug.cpp:12:  
    /usr/include/boost/numeric/odeint/stepper/controlled_runge_kutta.hpp: In instantiation of ‘boost::numeric::odeint::default_error_checker<Value, Algebra, Operations>::value_type boost::numeric::odeint::default_error_checker<Value, Algebra, Operations>::error(boost::numeric::odeint::default_error_checker<Value, Algebra, Operations>::algebra_type&, const State&, const Deriv&, Err&, Time) const [with State = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>; Deriv = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>; Err = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>; Time = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Value = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Algebra = boost::numeric::odeint::array_algebra; Operations = boost::numeric::odeint::default_operations; boost::numeric::odeint::default_error_checker<Value, Algebra, Operations>::value_type = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; boost::numeric::odeint::default_error_checker<Value, Algebra, Operations>::algebra_type = boost::numeric::odeint::array_algebra]’:  
    /usr/include/boost/numeric/odeint/stepper/controlled_runge_kutta.hpp:768:20:   required from ‘boost::numeric::odeint::controlled_step_result boost::numeric::odeint::controlled_runge_kutta<ErrorStepper, ErrorChecker, StepAdjuster, Resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>::try_step(System, const StateIn&, const DerivIn&, boost::numeric::odeint::controlled_runge_kutta<ErrorStepper, ErrorChecker, StepAdjuster, Resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>::time_type&, StateOut&, DerivOut&, boost::numeric::odeint::controlled_runge_kutta<ErrorStepper, ErrorChecker, StepAdjuster, Resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>::time_type&) [with System = Force; StateIn = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>; DerivIn = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>; StateOut = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>; DerivOut = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>; ErrorStepper = boost::numeric::odeint::runge_kutta_dopri5<std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >; ErrorChecker = boost::numeric::odeint::default_error_checker<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations>; StepAdjuster = boost::numeric::odeint::default_step_adjuster<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >; Resizer = boost::numeric::odeint::initially_resizer; boost::numeric::odeint::controlled_runge_kutta<ErrorStepper, ErrorChecker, StepAdjuster, Resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>::time_type = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>]’  
    /usr/include/boost/numeric/odeint/stepper/dense_output_runge_kutta.hpp:338:17:   required from ‘std::pair<typename Stepper::stepper_type::time_type, typename Stepper::stepper_type::time_type> boost::numeric::odeint::dense_output_runge_kutta<Stepper, boost::numeric::odeint::explicit_controlled_stepper_fsal_tag>::do_step(System) [with System = Force; Stepper = boost::numeric::odeint::controlled_runge_kutta<boost::numeric::odeint::runge_kutta_dopri5<std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >, boost::numeric::odeint::default_error_checker<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations>, boost::numeric::odeint::default_step_adjuster<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >, boost::numeric::odeint::initially_resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>; typename Stepper::stepper_type::time_type = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>]’  
    bug.cpp:51:27:   required from here  
    /usr/include/boost/numeric/odeint/stepper/controlled_runge_kutta.hpp:89:40: error: no matching function for call to ‘boost::numeric::odeint::array_algebra::norm_inf(std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 2>&)’  
       89 |         return algebra.norm_inf( x_err );  
	  |                                        ^  
    In file included from /usr/include/boost/numeric/odeint/algebra/algebra_dispatcher.hpp:29,  
		     from /usr/include/boost/numeric/odeint/stepper/euler.hpp:27,  
		     from /usr/include/boost/numeric/odeint.hpp:27,  
		     from bug.cpp:12:  
    /usr/include/boost/numeric/odeint/algebra/array_algebra.hpp:276:64: note: candidate: ‘template<template<class, long unsigned int <anonymous> > class Array, class T, long unsigned int dim> static typename boost::numeric::odeint::norm_result_type<Array<T, dim> >::type boost::numeric::odeint::array_algebra::norm_inf(const Array<T, dim>&)’  
      276 |     static typename norm_result_type< Array< T , dim > >::type norm_inf( const Array< T , dim > &s )  
	  |                                                                ^~~~~~~~  
    /usr/include/boost/numeric/odeint/algebra/array_algebra.hpp:276:64: note:   substitution of deduced template arguments resulted in errors seen above

---

## Comment 1

> Username: cffk  
> Created at: 2019-10-02 15:06:19 UTC  
> Url: https://github.com/boostorg/odeint/issues/40#issuecomment-537537305  

I tried my test program with various versions of boost.  I find  
  
* boost versions 1.67.0 and earlier compile and run OK  
* boost versions 1.68.0 and later result in the compiler error given above  
* this behavior is the same for g++ 8.3.1 and g++ 9.2.1  
  
So something rotten was introduced in boost 1.68.0.  Since there were no changes to odeint.hpp in 1.68.0, maybe the problem is with the multiprecision library.  I'll post an issue for that library.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-10-02 18:38:45 UTC  
> Url: https://github.com/boostorg/odeint/issues/40#issuecomment-537625286  

I think I see the issue:  
  
* In order to support complex numbers, class `boost::multiprecision::number` has acquired a nested `value_type` member.  The catch is that this is the same type as the class if the number type is not complex.  
* So this results in infinite recursion inside:  
  
```  
template< typename S >  
struct extract_value_type< S , typename boost::enable_if< has_value_type<S> >::type >  
{  
    // go down the value_type  
    typedef typename extract_value_type< typename S::value_type >::type type;  
};  
```  
  
I'm not sure what to do about this - there is no way (so far as I know) to only define the `::value_type` member in certain situations and not in others - at least not without resorting to additional base classes and such like which I'd rather not.  
  
One option might be to define a partial specialization of `extract_value_type` in Multiprecision (or indeed a partial specialization in OdeInt).  
  
Or, thinking out loud, 1) does `extract_value_type` really need to go right down the `::value_type` chain?  or 2) can it exclude the case that `T::value_type` is the same as `T`? I think an application of `mpl::eval_if_c` might do it?  
  
The simple fix for the OP is to add a full specialisation for `extract_value_type` on `float128`.

---

## Comment 3

> Username: cosurgi  
> Created at: 2020-03-30 12:49:47 UTC  
> Updated at: 2020-03-30 13:04:19 UTC  
> Url: https://github.com/boostorg/odeint/issues/40#issuecomment-605979075  

Any progress with that? yade stopped working on ubuntu focal 20.04, after it switched to boost 1.71 package. We just discovered it here: https://gitlab.com/yade-dev/trunk/-/merge_requests/450  
Affected are all types listed here: https://yade-dem.org/doc/HighPrecisionReal.html  
  
  
EDIT: in [related thread](https://github.com/headmyshoulder/odeint-v2/issues/245) is appears that the solution is to revert this commit: https://github.com/boostorg/multiprecision/commit/2f1cb020b08a1fbd0b8ea6ec26ff2928b3c7842a

---

## Comment 4

> Username: cosurgi  
> Created at: 2020-03-30 13:08:13 UTC  
> Updated at: 2020-03-30 13:08:55 UTC  
> Url: https://github.com/boostorg/odeint/issues/40#issuecomment-605987898  

> I'm not sure what to do about this - there is no way (so far as I know) to only define the ::value_type member in certain situations and not in others  
  
yes, this is true. In fact I have hit this problem earlier, before odeint introduced this bug. This is the reason why in yade I had to split thin wrapper for `long double` (it's an unrelated bug in boost::python - it treats long double as double, thereby losing 3 decimal digits of precision) into two classes. Almost a copy of each other. One for real, another for complex:  
  
* https://gitlab.com/yade-dev/trunk/-/blob/master/lib/high-precision/ThinRealWrapper.hpp  
* https://gitlab.com/yade-dev/trunk/-/blob/master/lib/high-precision/ThinComplexWrapper.hpp

---

## Comment 5

> Username: cosurgi  
> Created at: 2020-03-31 16:13:32 UTC  
> Updated at: 2020-04-01 12:15:53 UTC  
> Url: https://github.com/boostorg/odeint/issues/40#issuecomment-606726861  

I have used [this workaround](https://gitlab.com/yade-dev/trunk/-/commit/359ad6afcd1c18060860fd5abe142660441d7d7d)  
  
EDIT: Maybe it's possible to stop infinite recursion with `if constexpr(std::is_same<…>::value)` ? or some variant thereof: maybe template specialization if both types inside and outiside are the same.

---

## Comment 6

> Username: tueda  
> Created at: 2020-11-12 11:45:41 UTC  
> Url: https://github.com/boostorg/odeint/issues/40#issuecomment-726028420  

Indeed, a patch to fix this issue has already been in PR, https://github.com/boostorg/odeint/pull/34.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2021-07-29 16:47:59 UTC  
> Url: https://github.com/boostorg/odeint/issues/40#issuecomment-889302680  

Is there any progress on fixing odeint for this?

---

## Comment 8

> Username: calvincramer  
> Created at: 2021-11-18 00:05:03 UTC  
> Url: https://github.com/boostorg/odeint/issues/40#issuecomment-972347182  

+1 for @jzmaddock's comment. Any progress?
