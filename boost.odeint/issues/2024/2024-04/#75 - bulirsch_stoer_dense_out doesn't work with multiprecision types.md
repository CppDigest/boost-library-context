# #75 - bulirsch_stoer_dense_out doesn't work with multiprecision types [Closed]

> Username: cffk  
> Created at: 2024-04-27 22:25:43 UTC  
> Updated at: 2024-05-22 15:12:28 UTC  
> Closed at: 2024-05-22 15:12:28 UTC  
> Url: https://github.com/boostorg/odeint/issues/75  

I want to use bulirsch_stoer with multiprecision types.  However doing the "obvious" thing with the sample code elliptic_functions.cpp results in a compile-time error.  
  
I'm using:  
* Linux Fedora 39  
* g++ (GCC) 13.2.1 20240316 (Red Hat 13.2.1-7)  
* boost boost-1.81.0-8.fc39.x86_64  
  
It *looks* like `algebra.norm_inf` needs to be extended to work with multiprecision types.  
  
Here is the test code `elliptic_function.cpp`  
```c++  
/*  
 * Adapted from sample code elliptic_functions.cpp in repo  
 * git@github.com:headmyshoulder/odeint-v2.git  
 *  
 * Compile with one of  
 *   g++ -o ell -DPRECISION=2 elliptic_functions.cpp // OK  
 *   g++ -o ell -DPRECISION=3 elliptic_functions.cpp // OK  
 *   g++ -o ell -DPRECISION=4 elliptic_functions.cpp // compile error  
 *   g++ -o ell -DPRECISION=5 elliptic_functions.cpp // compile error  
 */  
  
#include <iostream>  
#include <cmath>  
#include <array>  
  
#include <boost/numeric/odeint/config.hpp>  
  
#include <boost/numeric/odeint.hpp>  
#include <boost/numeric/odeint/stepper/bulirsch_stoer.hpp>  
#include <boost/numeric/odeint/stepper/bulirsch_stoer_dense_out.hpp>  
  
#if PRECISION == 3  
// This is OK  
typedef long double value_type;  
#elif PRECISION == 4  
// This gives compile failure  
#include <boost/multiprecision/float128.hpp>  
typedef boost::multiprecision::float128 value_type;  
#elif PRECISION == 5  
// This gives compile failure  
#include <boost/multiprecision/cpp_dec_float.hpp>  
typedef boost::multiprecision::cpp_dec_float_50 value_type;  
#else  
// This is OK  
typedef double value_type;  
#endif  
  
typedef std::array< value_type , 3 > state_type;  
  
using namespace std;  
using namespace boost::numeric::odeint;  
  
/*  
 * x1' = x2*x3  
 * x2' = -x1*x3  
 * x3' = -m*x1*x2  
 */  
  
void rhs( const state_type &x, state_type &dxdt, const value_type /*t*/ ) {  
  static const value_type m = 51/value_type(100);  
  
  dxdt[0] = x[1]*x[2];  
  dxdt[1] = -x[0]*x[2];  
  dxdt[2] = -m*x[0]*x[1];  
}  
  
int main() {  
  bulirsch_stoer_dense_out< state_type, value_type >  
    stepper( value_type(1E-9), value_type(1E-9), value_type(1), value_type(0) );  
  
  state_type x1 = { value_type(0), value_type(1), value_type(1) };  
  
  value_type t = 0.0;  
  value_type dt = 0.01;  
  
  integrate_adaptive( stepper, rhs, x1, t, value_type(100), dt );  
  cout << x1[0] << " " << x1[1] << " " << x1[2] << endl;  
}  
```  
  
Compiling this with, e.g.,  
```bash  
g++ -c -DPRECISION=4 elliptic_functions.cpp  
```  
gives  
```  
In file included from /usr/include/boost/numeric/odeint/algebra/norm_result_type.hpp:20,  
                 from /usr/include/boost/numeric/odeint/algebra/range_algebra.hpp:28,  
                 from /usr/include/boost/numeric/odeint/stepper/euler.hpp:25,  
                 from /usr/include/boost/numeric/odeint.hpp:27,  
                 from elliptic_functions.cpp:18:  
/usr/include/boost/numeric/odeint/algebra/detail/extract_value_type.hpp: In instantiation of ‘struct boost::numeric::odeint::detail::extract_value_type<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, void>’:  
/usr/include/boost/numeric/odeint/algebra/detail/extract_value_type.hpp:47:73:   required from ‘struct boost::numeric::odeint::detail::extract_value_type<std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>, void>’  
/usr/include/boost/numeric/odeint/algebra/norm_result_type.hpp:28:60:   required from ‘struct boost::numeric::odeint::norm_result_type<std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>, void>’  
/usr/include/boost/numeric/odeint/algebra/array_algebra.hpp:276:64:   required by substitution of ‘template<template<class, long unsigned int <anonymous> > class Array, class T, long unsigned int dim> static typename boost::numeric::odeint::norm_result_type<Array<T, dim> >::type boost::numeric::odeint::array_algebra::norm_inf(const Array<T, dim>&) [with Array = std::array; T = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; long unsigned int dim = 3]’  
/usr/include/boost/numeric/odeint/stepper/controlled_runge_kutta.hpp:89:32:   required from ‘boost::numeric::odeint::default_error_checker<Value, Algebra, Operations>::value_type boost::numeric::odeint::default_error_checker<Value, Algebra, Operations>::error(algebra_type&, const State&, const Deriv&, Err&, Time) const [with State = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Deriv = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Err = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Time = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Value = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Algebra = boost::numeric::odeint::array_algebra; Operations = boost::numeric::odeint::default_operations; value_type = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; algebra_type = boost::numeric::odeint::array_algebra]’  
/usr/include/boost/numeric/odeint/stepper/bulirsch_stoer_dense_out.hpp:199:63:   required from ‘boost::numeric::odeint::controlled_step_result boost::numeric::odeint::bulirsch_stoer_dense_out<State, Value, Deriv, Time, Algebra, Operations, Resizer>::try_step(System, const StateIn&, const DerivIn&, time_type&, StateOut&, DerivOut&, time_type&) [with System = void (*)(const std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>); StateIn = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; DerivIn = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; StateOut = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; DerivOut = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; State = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Value = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Deriv = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Time = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Algebra = boost::numeric::odeint::array_algebra; Operations = boost::numeric::odeint::default_operations; Resizer = boost::numeric::odeint::initially_resizer; time_type = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>]’  
/usr/include/boost/numeric/odeint/stepper/bulirsch_stoer_dense_out.hpp:339:27:   required from ‘std::pair<_Size, _Size> boost::numeric::odeint::bulirsch_stoer_dense_out<State, Value, Deriv, Time, Algebra, Operations, Resizer>::do_step(System) [with System = void (*)(const std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>); State = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Value = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Deriv = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Time = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Algebra = boost::numeric::odeint::array_algebra; Operations = boost::numeric::odeint::default_operations; Resizer = boost::numeric::odeint::initially_resizer]’  
/usr/include/boost/numeric/odeint/integrate/detail/integrate_adaptive.hpp:140:23:   required from ‘size_t boost::numeric::odeint::detail::integrate_adaptive(Stepper, System, State&, Time, Time, Time, Observer, boost::numeric::odeint::dense_output_stepper_tag) [with Stepper = boost::numeric::odeint::bulirsch_stoer_dense_out<std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >; System = void (*)(const std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>); State = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Time = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Observer = boost::numeric::odeint::null_observer; size_t = long unsigned int]’  
/usr/include/boost/numeric/odeint/integrate/integrate_adaptive.hpp:42:38:   required from ‘size_t boost::numeric::odeint::integrate_adaptive(Stepper, System, State&, Time, Time, Time, Observer) [with Stepper = bulirsch_stoer_dense_out<std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >; System = void (*)(const std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>); State = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Time = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Observer = null_observer; size_t = long unsigned int]’  
/usr/include/boost/numeric/odeint/integrate/integrate_adaptive.hpp:83:30:   required from ‘size_t boost::numeric::odeint::integrate_adaptive(Stepper, System, State&, Time, Time, Time) [with Stepper = bulirsch_stoer_dense_out<std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >; System = void (*)(const std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>); State = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Time = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; size_t = long unsigned int]’  
elliptic_functions.cpp:66:21:   required from here  
/usr/include/boost/numeric/odeint/algebra/detail/extract_value_type.hpp:47:73: error: invalid use of incomplete type ‘struct boost::numeric::odeint::detail::extract_value_type<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, void>’  
   47 |     typedef typename extract_value_type< typename S::value_type >::type type;  
      |                                                                         ^~~~  
/usr/include/boost/numeric/odeint/algebra/detail/extract_value_type.hpp:44:8: note: definition of ‘struct boost::numeric::odeint::detail::extract_value_type<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, void>’ is not complete until the closing brace  
   44 | struct extract_value_type< S , typename boost::enable_if< has_value_type<S> >::type >  
      |        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/include/boost/numeric/odeint.hpp:35:  
/usr/include/boost/numeric/odeint/stepper/controlled_runge_kutta.hpp: In instantiation of ‘boost::numeric::odeint::default_error_checker<Value, Algebra, Operations>::value_type boost::numeric::odeint::default_error_checker<Value, Algebra, Operations>::error(algebra_type&, const State&, const Deriv&, Err&, Time) const [with State = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Deriv = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Err = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Time = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Value = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Algebra = boost::numeric::odeint::array_algebra; Operations = boost::numeric::odeint::default_operations; value_type = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; algebra_type = boost::numeric::odeint::array_algebra]’:  
/usr/include/boost/numeric/odeint/stepper/bulirsch_stoer_dense_out.hpp:199:63:   required from ‘boost::numeric::odeint::controlled_step_result boost::numeric::odeint::bulirsch_stoer_dense_out<State, Value, Deriv, Time, Algebra, Operations, Resizer>::try_step(System, const StateIn&, const DerivIn&, time_type&, StateOut&, DerivOut&, time_type&) [with System = void (*)(const std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>); StateIn = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; DerivIn = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; StateOut = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; DerivOut = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; State = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Value = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Deriv = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Time = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Algebra = boost::numeric::odeint::array_algebra; Operations = boost::numeric::odeint::default_operations; Resizer = boost::numeric::odeint::initially_resizer; time_type = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>]’  
/usr/include/boost/numeric/odeint/stepper/bulirsch_stoer_dense_out.hpp:339:27:   required from ‘std::pair<_Size, _Size> boost::numeric::odeint::bulirsch_stoer_dense_out<State, Value, Deriv, Time, Algebra, Operations, Resizer>::do_step(System) [with System = void (*)(const std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>); State = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Value = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Deriv = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Time = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Algebra = boost::numeric::odeint::array_algebra; Operations = boost::numeric::odeint::default_operations; Resizer = boost::numeric::odeint::initially_resizer]’  
/usr/include/boost/numeric/odeint/integrate/detail/integrate_adaptive.hpp:140:23:   required from ‘size_t boost::numeric::odeint::detail::integrate_adaptive(Stepper, System, State&, Time, Time, Time, Observer, boost::numeric::odeint::dense_output_stepper_tag) [with Stepper = boost::numeric::odeint::bulirsch_stoer_dense_out<std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >; System = void (*)(const std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>); State = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Time = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Observer = boost::numeric::odeint::null_observer; size_t = long unsigned int]’  
/usr/include/boost/numeric/odeint/integrate/integrate_adaptive.hpp:42:38:   required from ‘size_t boost::numeric::odeint::integrate_adaptive(Stepper, System, State&, Time, Time, Time, Observer) [with Stepper = bulirsch_stoer_dense_out<std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >; System = void (*)(const std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>); State = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Time = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; Observer = null_observer; size_t = long unsigned int]’  
/usr/include/boost/numeric/odeint/integrate/integrate_adaptive.hpp:83:30:   required from ‘size_t boost::numeric::odeint::integrate_adaptive(Stepper, System, State&, Time, Time, Time) [with Stepper = bulirsch_stoer_dense_out<std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >; System = void (*)(const std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>); State = std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>; Time = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; size_t = long unsigned int]’  
elliptic_functions.cpp:66:21:   required from here  
/usr/include/boost/numeric/odeint/stepper/controlled_runge_kutta.hpp:89:32: error: no matching function for call to ‘boost::numeric::odeint::array_algebra::norm_inf(std::array<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>, 3>&)’  
   89 |         return algebra.norm_inf( x_err );  
      |                ~~~~~~~~~~~~~~~~^~~~~~~~~  
In file included from /usr/include/boost/numeric/odeint/algebra/algebra_dispatcher.hpp:29,  
                 from /usr/include/boost/numeric/odeint/stepper/euler.hpp:27:  
/usr/include/boost/numeric/odeint/algebra/array_algebra.hpp:276:64: note: candidate: ‘template<template<class, long unsigned int <anonymous> > class Array, class T, long unsigned int dim> static typename boost::numeric::odeint::norm_result_type<Array<T, dim> >::type boost::numeric::odeint::array_algebra::norm_inf(const Array<T, dim>&)’  
  276 |     static typename norm_result_type< Array< T , dim > >::type norm_inf( const Array< T , dim > &s )  
      |                                                                ^~~~~~~~  
/usr/include/boost/numeric/odeint/algebra/array_algebra.hpp:276:64: note:   substitution of deduced template arguments resulted in errors seen above  
```

---

## Comment 1

> Username: cffk  
> Created at: 2024-04-28 14:10:55 UTC  
> Url: https://github.com/boostorg/odeint/issues/75#issuecomment-2081496999  

The following patch  
```patch  
--- /usr/include/boost/numeric/odeint/stepper/controlled_runge_kutta.hpp~	2023-08-19 20:00:00.000000000 -0400  
+++ /usr/include/boost/numeric/odeint/stepper/controlled_runge_kutta.hpp	2024-04-28 09:52:51.722411456 -0400  
@@ -86,7 +86,12 @@  
   
         // value_type res = algebra.reduce( x_err ,  
         //        typename operations_type::template maximum< value_type >() , static_cast< value_type >( 0 ) );  
-        return algebra.norm_inf( x_err );  
+        // return algebra.norm_inf( x_err );  
+        value_type res = 0;  
+        using std::max;  
+        for (auto it = x_err.begin(); it != x_err.end(); ++it)  
+          res = max( res, abs( *it ) );  
+        return res;  
     }  
   
 private:  
```  
fixes my problem.  This clearly isn't the optimal solution; it merely open-codes `norm_inf` and so avoids my figuring out boost's template resolution.

---

## Comment 2

> Username: cffk  
> Created at: 2024-05-22 13:21:59 UTC  
> Url: https://github.com/boostorg/odeint/issues/75#issuecomment-2124787372  

This issue overlaps with my issue #40 and very likely PR for that issue, #63, fixes this issue too.  How can I find out which version of boost has this PR applied?

---

## Comment 3

> Username: mborland  
> Created at: 2024-05-22 13:42:09 UTC  
> Url: https://github.com/boostorg/odeint/issues/75#issuecomment-2124833555  

> This issue overlaps with my issue #40 and very likely PR for that issue, #63, fixes this issue too. How can I find out which version of boost has this PR applied?  
  
1.85 is when it was applied. If you look at a particular commit towards the top it'll show which tags (i.e. boost versions) it appears on: https://github.com/boostorg/odeint/pull/63/commits/68950d8df2a8be067821878ed2e6ca363d0f411d. If you still have an issue with that version let me know.

---

## Comment 4

> Username: cffk  
> Created at: 2024-05-22 15:12:28 UTC  
> Url: https://github.com/boostorg/odeint/issues/75#issuecomment-2125052670  

Many thanks.  I'll close this issue for now.
