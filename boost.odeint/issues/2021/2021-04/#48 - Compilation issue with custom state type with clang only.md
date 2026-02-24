# #48 - Compilation issue with custom state type with clang only [Open]

> Username: mreininghaus  
> Created at: 2021-04-27 13:30:18 UTC  
> Updated at: 2021-04-27 13:34:31 UTC  
> Url: https://github.com/boostorg/odeint/issues/48  

In the code below, I am working with a custom state-type (a `std::pair` in this case). The code compiles with g++, icc, msvc. With clang however, compilation fails. In order to get it to build successfully, I apparently need to define `operator+()` and `operator*()` for the state type before including the odeint header. Link to godbolt: https://godbolt.org/z/6eavzdxbf  
  
```c++  
#include <cstdlib>  
#include <utility>  
  
#include <boost/numeric/odeint.hpp> // if included here, compilations fails with clang++  
  
using state_type = std::pair<double, double>;  
using deriv_type = state_type;  
using time_type = double;  
  
state_type operator+(state_type a, state_type const& b);  
state_type operator*(state_type a, time_type b);  
state_type operator*(time_type b, state_type a);  
  
//~ #include <boost/numeric/odeint.hpp> // if included here, no error!  
  
state_type operator+(state_type a, state_type const& b) {  
	a.first += b.first;  
	a.second += b.second;  
	return a;  
}  
  
state_type operator*(state_type a, time_type b) {  
	a.first *= b;  
	a.second *= b;  
	return a;  
}  
  
state_type operator*(time_type b, state_type a) {  
	return a * b;  
}  
  
void harmonic_oscillator(const state_type& x, deriv_type& dxdt, time_type) {  
	static auto constexpr gam = 0.1;  
  
	dxdt.first = x.second;  
	dxdt.second = -x.first - gam*x.second;  
}  
  
namespace bno = boost::numeric::odeint;  
using stepper_type = bno::runge_kutta_dopri5<state_type, double, deriv_type, time_type, bno::vector_space_algebra>;  
  
int main() {  
	state_type x0{1.0, 0.0};  
	  
	// just to see if the operators work...  
	x0 * 1.;  
	2. * x0;  
	x0 + x0;  
	  
	stepper_type stepper;  
	time_type const dt = 0.005;  
	for (time_type t = 0; t < 10; t += dt) {  
		stepper.do_step(harmonic_oscillator, x0, t, dt);  
		std::cout << t << '\t' << x0.first << '\t' << x0.second << '\n';  
	}  
	  
	return EXIT_SUCCESS;  
}  
```
