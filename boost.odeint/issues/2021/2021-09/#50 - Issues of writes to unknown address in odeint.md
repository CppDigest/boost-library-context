# #50 - Issues of writes to unknown address in odeint [Open]

> Username: bhache  
> Created at: 2021-09-03 14:49:02 UTC  
> Updated at: 2021-09-03 14:51:46 UTC  
> Url: https://github.com/boostorg/odeint/issues/50  

I am trying to use boost library's odeint with `std::vector` or `std::array`. I have no issues with compilation in either case, however, I have runtime issues regarding write to unknown address in the case of `std::array`. Given below is a MWE.   
  
I have defined two classes: `ODE1`, and `ODE2` with `std::vector`, and `std::array` respectively in the header file `ode.hpp`.  
  
```  
#ifndef ODE_HPP_                                                                     
#define ODE_HPP_                                                                     
                                                                          
// c++ standard library headers                                                      
#include <array>                                                                     
#include <iostream>                                                                  
#include <vector>                                                                    
                                                                                       
namespace ode {                                                                      
constexpr size_t n_sections = 1800;                                                  
constexpr size_t n_nodes_per_section = 6;                                            
constexpr size_t n_variables = n_sections * n_nodes_per_section;                     
}  // namespace ode                                                                  
                                                                                       
class ODE1 {                                                                         
 public:                                                                             
  ODE1(const double o) : V_(ode::n_variables, o) {}  // explicit constructor         
                                                                                    
  std::vector<double>& GetVoltage() { return V_; }                                
                                                                                    
  void operator()(const std::vector<double>& Y, std::vector<double>& dYdt,        
                  const double /* time */) {                                         
    using namespace ode;                                                             
    for (size_t n = 0; n < n_sections; n++) {                                        
      dYdt[n * n_nodes_per_section + 0] = Y[n * n_nodes_per_section + 0];            
      dYdt[n * n_nodes_per_section + 1] = Y[n * n_nodes_per_section + 1];            
      dYdt[n * n_nodes_per_section + 2] = Y[n * n_nodes_per_section + 2];  
      dYdt[n * n_nodes_per_section + 3] = Y[n * n_nodes_per_section + 3];            
      dYdt[n * n_nodes_per_section + 4] = Y[n * n_nodes_per_section + 4];            
      dYdt[n * n_nodes_per_section + 5] = Y[n * n_nodes_per_section + 5];            
    }                                                                                
  }                                                                                  
                                                                                       
 private:                                                                            
  std::vector<double> V_{};                                                          
};                                                                                   
  
class ODE2 {                                                                         
 using Array = std::array<double, ode::n_variables>;                                
                                                                                      
 public:                                                                             
  // constructors                                                                    
  ODE2(const double o) { V_.fill(o); }  // explicit constructor                                                         
  
  Array& GetVoltage() { return V_; }                                              
                                                                                    
  void operator()(const Array& Y, Array& dYdt, const double /* time */) {         
    using namespace ode;                                                          
    for (size_t n = 0; n < n_sections; n++) {                                     
      dYdt[n * n_nodes_per_section + 0] = Y[n * n_nodes_per_section + 0];         
      dYdt[n * n_nodes_per_section + 1] = Y[n * n_nodes_per_section + 1];         
      dYdt[n * n_nodes_per_section + 2] = Y[n * n_nodes_per_section + 2];         
      dYdt[n * n_nodes_per_section + 3] = Y[n * n_nodes_per_section + 3];         
      dYdt[n * n_nodes_per_section + 4] = Y[n * n_nodes_per_section + 4];         
      dYdt[n * n_nodes_per_section + 5] = Y[n * n_nodes_per_section + 5];         
    }                                                                             
  }                                                                               
                                                                                    
 private:                                                                         
  Array V_{};                                                                     
};                                                                                
#endif  //   ODE_HPP_                                                                                                                                                                                                                                         
```  
  
And the main function is defined in the file `ode.cpp'  
```  
// related header                                                                    
#include "ode.hpp"                                                                   
  
// other library headers                                                             
#include <boost/numeric/odeint.hpp>                                                  
                                                                                                                                                                            
int main() {                                                                         
  constexpr double abs_err = 1.0e-10;                                                
  constexpr double rel_err = 1.0e-8;                                                 
  constexpr double sim_duration = 20.0;              // ms                           
  constexpr double t_start = 0;                      // ms                           
  constexpr double t_stop = t_start + sim_duration;  // ms                           
  constexpr double dt = 5e-3;                        // ms                           
  {                                                                                  
    std::cout << "ODE 1: ";                                                          
    using namespace boost::numeric::odeint;                                          
    using error_stepper_type = runge_kutta_dopri5<std::vector<double>>;              
                                                                                       
    ODE1 o(-60.0);                                                                   
    integrate_adaptive(make_controlled<error_stepper_type>(abs_err, rel_err), o,  
                       o.GetVoltage(), t_start, t_stop, dt);                         
  }                                                                                  
  {                                                                                  
    std::cout << "ODE 1: ";                                                          
    using namespace boost::numeric::odeint;                                          
    using error_stepper_type =                                                       
          runge_kutta_dopri5<std::array<double, ode::n_variables>>;                    
                                                                                       
    ODE2 o(-60.0);                                                                   
    integrate_adaptive(make_controlled<error_stepper_type>(abs_err, rel_err), o,  
                       o.GetVoltage(), t_start, t_stop, dt);                         
  }                                                                                  
  return 0;                                                                          
}     
```  
  
I am compiling using the clang compiler as follows  
`c++ -O0 -g -fsanitize=address -fno-omit-frame-pointer -Wall -Wextra -Wpedantic -std=c++17 ode.cpp -o ode`  
  
Compilation output (warnings)  
```  
In file included from ode.cpp:5:  
In file included from /usr/local/include/boost/numeric/odeint.hpp:79:  
In file included from /usr/local/include/boost/numeric/odeint/stepper/generation.hpp:32:  
/usr/local/include/boost/numeric/odeint/stepper/generation/generation_controlled_adams_bashforth_moulton.hpp:43:99: warning: unused parameter 'stepper' [-Wunused-parameter]  
    controller_type operator()( value_type abs_error , value_type rel_error , const stepper_type &stepper )  
                                                                                                  ^  
/usr/local/include/boost/numeric/odeint/stepper/generation/generation_controlled_adams_bashforth_moulton.hpp:49:71: warning: unused parameter 'stepper' [-Wunused-parameter]  
                                time_type max_dt, const stepper_type &stepper )  
                                                                      ^  
2 warnings generated.  
```  
  
As I mentioned above, I do not run into issues with compilation. When I run the executable, I have the following issues  
```  
AddressSanitizer:DEADLYSIGNAL  
=================================================================  
==29478==ERROR: AddressSanitizer: stack-overflow on address 0x7ffee1db40e0 (pc 0x00010d66140c bp 0x7ffee1dde910 sp 0x7ffee1da5d80 T0)  
    #0 0x10d66140c in void boost::numeric::odeint::runge_kutta_dopri5<std::__1::array<double, 7200ul>, double, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations, boost::numeric::odeint::initially_resizer>::do_step_impl<ODE2, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul> >(ODE2, std::__1::array<double, 7200ul> const&, std::__1::array<double, 7200ul> const&, double, std::__1::array<double, 7200ul>&, std::__1::array<double, 7200ul>&, double, std::__1::array<double, 7200ul>&) runge_kutta_dopri5.hpp:165  
    #1 0x10d660d9c in void boost::numeric::odeint::explicit_error_stepper_fsal_base<boost::numeric::odeint::runge_kutta_dopri5<std::__1::array<double, 7200ul>, double, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations, boost::numeric::odeint::initially_resizer>, (unsigned short)5, (unsigned short)5, (unsigned short)4, std::__1::array<double, 7200ul>, double, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations, boost::numeric::odeint::initially_resizer>::do_step<ODE2, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul> >(ODE2, std::__1::array<double, 7200ul> const&, std::__1::array<double, 7200ul> const&, double, std::__1::array<double, 7200ul>&, std::__1::array<double, 7200ul>&, double, std::__1::array<double, 7200ul>&) explicit_error_stepper_fsal_base.hpp:272  
    #2 0x10d660451 in boost::numeric::odeint::controlled_step_result boost::numeric::odeint::controlled_runge_kutta<boost::numeric::odeint::runge_kutta_dopri5<std::__1::array<double, 7200ul>, double, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations, boost::numeric::odeint::initially_resizer>, boost::numeric::odeint::default_error_checker<double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations>, boost::numeric::odeint::default_step_adjuster<double, double>, boost::numeric::odeint::initially_resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>::try_step<ODE2, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul> >(ODE2, std::__1::array<double, 7200ul> const&, std::__1::array<double, 7200ul> const&, double&, std::__1::array<double, 7200ul>&, std::__1::array<double, 7200ul>&, double&) controlled_runge_kutta.hpp:768  
    #3 0x10d65ea18 in boost::numeric::odeint::controlled_step_result boost::numeric::odeint::controlled_runge_kutta<boost::numeric::odeint::runge_kutta_dopri5<std::__1::array<double, 7200ul>, double, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations, boost::numeric::odeint::initially_resizer>, boost::numeric::odeint::default_error_checker<double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations>, boost::numeric::odeint::default_step_adjuster<double, double>, boost::numeric::odeint::initially_resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>::try_step<ODE2, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul> >(ODE2, std::__1::array<double, 7200ul>&, std::__1::array<double, 7200ul>&, double&, double&) controlled_runge_kutta.hpp:718  
    #4 0x10d65d9b1 in boost::numeric::odeint::controlled_step_result boost::numeric::odeint::controlled_runge_kutta<boost::numeric::odeint::runge_kutta_dopri5<std::__1::array<double, 7200ul>, double, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations, boost::numeric::odeint::initially_resizer>, boost::numeric::odeint::default_error_checker<double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations>, boost::numeric::odeint::default_step_adjuster<double, double>, boost::numeric::odeint::initially_resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>::try_step_v1<ODE2, std::__1::array<double, 7200ul> >(ODE2, std::__1::array<double, 7200ul>&, double&, double&) controlled_runge_kutta.hpp:902  
    #5 0x10d65d112 in boost::numeric::odeint::controlled_step_result boost::numeric::odeint::controlled_runge_kutta<boost::numeric::odeint::runge_kutta_dopri5<std::__1::array<double, 7200ul>, double, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations, boost::numeric::odeint::initially_resizer>, boost::numeric::odeint::default_error_checker<double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations>, boost::numeric::odeint::default_step_adjuster<double, double>, boost::numeric::odeint::initially_resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>::try_step<ODE2, std::__1::array<double, 7200ul> >(ODE2, std::__1::array<double, 7200ul>&, double&, double&) controlled_runge_kutta.hpp:619  
    #6 0x10d65cc3e in unsigned long boost::numeric::odeint::detail::integrate_adaptive<boost::numeric::odeint::controlled_runge_kutta<boost::numeric::odeint::runge_kutta_dopri5<std::__1::array<double, 7200ul>, double, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations, boost::numeric::odeint::initially_resizer>, boost::numeric::odeint::default_error_checker<double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations>, boost::numeric::odeint::default_step_adjuster<double, double>, boost::numeric::odeint::initially_resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>, ODE2, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::null_observer>(boost::numeric::odeint::controlled_runge_kutta<boost::numeric::odeint::runge_kutta_dopri5<std::__1::array<double, 7200ul>, double, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations, boost::numeric::odeint::initially_resizer>, boost::numeric::odeint::default_error_checker<double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations>, boost::numeric::odeint::default_step_adjuster<double, double>, boost::numeric::odeint::initially_resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>, ODE2, std::__1::array<double, 7200ul>&, double&, double, double&, boost::numeric::odeint::null_observer, boost::numeric::odeint::controlled_stepper_tag) integrate_adaptive.hpp:103  
    #7 0x10d65c401 in unsigned long boost::numeric::odeint::integrate_adaptive<boost::numeric::odeint::controlled_runge_kutta<boost::numeric::odeint::runge_kutta_dopri5<std::__1::array<double, 7200ul>, double, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations, boost::numeric::odeint::initially_resizer>, boost::numeric::odeint::default_error_checker<double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations>, boost::numeric::odeint::default_step_adjuster<double, double>, boost::numeric::odeint::initially_resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>, ODE2, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::null_observer>(boost::numeric::odeint::controlled_runge_kutta<boost::numeric::odeint::runge_kutta_dopri5<std::__1::array<double, 7200ul>, double, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations, boost::numeric::odeint::initially_resizer>, boost::numeric::odeint::default_error_checker<double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations>, boost::numeric::odeint::default_step_adjuster<double, double>, boost::numeric::odeint::initially_resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>, ODE2, std::__1::array<double, 7200ul>&, double, double, double, boost::numeric::odeint::null_observer) integrate_adaptive.hpp:42  
    #8 0x10d63acde in unsigned long boost::numeric::odeint::integrate_adaptive<boost::numeric::odeint::controlled_runge_kutta<boost::numeric::odeint::runge_kutta_dopri5<std::__1::array<double, 7200ul>, double, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations, boost::numeric::odeint::initially_resizer>, boost::numeric::odeint::default_error_checker<double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations>, boost::numeric::odeint::default_step_adjuster<double, double>, boost::numeric::odeint::initially_resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>, ODE2, std::__1::array<double, 7200ul>, double>(boost::numeric::odeint::controlled_runge_kutta<boost::numeric::odeint::runge_kutta_dopri5<std::__1::array<double, 7200ul>, double, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations, boost::numeric::odeint::initially_resizer>, boost::numeric::odeint::default_error_checker<double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations>, boost::numeric::odeint::default_step_adjuster<double, double>, boost::numeric::odeint::initially_resizer, boost::numeric::odeint::explicit_error_stepper_fsal_tag>, ODE2, std::__1::array<double, 7200ul>&, double, double, double) integrate_adaptive.hpp:83  
    #9 0x10d639fac in main ode.cpp:30  
    #10 0x7fff203d0f3c in start+0x0 (libdyld.dylib:x86_64+0x15f3c)  
  
SUMMARY: AddressSanitizer: stack-overflow runge_kutta_dopri5.hpp:165 in void boost::numeric::odeint::runge_kutta_dopri5<std::__1::array<double, 7200ul>, double, std::__1::array<double, 7200ul>, double, boost::numeric::odeint::array_algebra, boost::numeric::odeint::default_operations, boost::numeric::odeint::initially_resizer>::do_step_impl<ODE2, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul>, std::__1::array<double, 7200ul> >(ODE2, std::__1::array<double, 7200ul> const&, std::__1::array<double, 7200ul> const&, double, std::__1::array<double, 7200ul>&, std::__1::array<double, 7200ul>&, double, std::__1::array<double, 7200ul>&)  
==29478==ABORTING  
ODE 1: ODE 1: zsh: abort      ./ode  
```  
  
Strange enough, I do not have issues when I change the parameter, `n_sections`, in the header from 1800 to 1000. I could not figure out the issue, is this something to with the boost library containers? Any help would be greatly appreciated.

---

## Comment 1

> Username: ddemidov  
> Created at: 2021-09-03 14:51:46 UTC  
> Url: https://github.com/boostorg/odeint/issues/50#issuecomment-912597981  

The error says stack overflow, and `std::array` is allocated on stack, so could that be an issue? The fact that smaller problem fits into the stack seems to also suggest this.
