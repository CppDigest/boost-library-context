# #92 - Using autodiff types for ODEint solvers [Open]

> Username: simonbeyer1  
> Created at: 2025-08-23 15:32:07 UTC  
> Updated at: 2025-08-26 10:57:19 UTC  
> Url: https://github.com/boostorg/odeint/issues/92  

I am exploring whether it is possible to compute first order (or even higher order) sensitivities of ODE solutions using Boost.ODEInt by giving the integrator an automatic differentiation type (like autodiff::dual or similar) as the state type.  
  
My questions are:  
  
- Is it feasible to use generic autodiff types in adaptive Runge-Kutta solvers or in stiff solvers provided by ODEInt to compute sensitivities directly?   
  
- Are there recommended practices or caveats when doing so?  
  
Any guidance, examples, or references would be greatly appreciated.  
  
Thank you very much!

---

## Comment 1

> Username: simonbeyer1  
> Created at: 2025-08-26 10:57:19 UTC  
> Url: https://github.com/boostorg/odeint/issues/92#issuecomment-3223678087  

In case others have the same question, I created a small example where the Van der Pol oscillator is integrated using the `CppAD::AD<double>` type. Currently, this only works with the fixed-step Runge-Kutta-Dopri5 method.  
  
Below is the C++ code and an example of how to call it from R:  
  
```cpp  
// [[Rcpp::plugins(cpp17)]]  
// [[Rcpp::depends(RcppEigen)]]  
// [[Rcpp::depends(BH)]]  
#include <RcppEigen.h>  
#include <Rcpp.h>  
#include <boost/numeric/odeint.hpp>  
#include <cppad/cppad.hpp>  
#include <vector>  
  
using namespace Rcpp;  
using namespace boost::numeric::odeint;  
using CppAD::AD;  
  
// Van der Pol ODE for CppAD  
struct vdp_system {  
  AD<double> mu;  
  vdp_system(AD<double> mu_) : mu(mu_) {}  
    
  template <typename StateType>  
  void operator()(const StateType &x, StateType &dxdt, double /*t*/) {  
    dxdt[0] = x[1];  
    dxdt[1] = mu * (1.0 - x[0]*x[0]) * x[1] - x[0];  
  }  
};  
  
// Observer to store trajectory  
template <typename StateType>  
struct push_back_state_and_time {  
  std::vector<StateType> &m_states;  
  std::vector<double> &m_times;  
    
  push_back_state_and_time(std::vector<StateType> &states,  
                           std::vector<double> &times)  
    : m_states(states), m_times(times) {}  
    
  void operator()(const StateType &x, double t) {  
    m_states.push_back(x);  
    m_times.push_back(t);  
  }  
};  
  
// [[Rcpp::export]]  
NumericMatrix integrate_vdp_rk_sens(NumericVector times, NumericVector params) {  
    
  // Parameters  
  double x0_val = params["x"];  
  double v0_val = params["v"];  
  double mu_val = params["mu"];  
    
  // CppAD variables for sensitivities  
  std::vector< AD<double> > X(3);  
  X[0] = x0_val;  // x0  
  X[1] = v0_val;  // v0  
  X[2] = mu_val;  // mu  
  CppAD::Independent(X);  
    
  AD<double> x0 = X[0];  
  AD<double> v0 = X[1];  
  AD<double> mu = X[2];  
    
  // Initial state  
  std::vector< AD<double> > x(2);  
  x[0] = x0;  
  x[1] = v0;  
    
  // Stepper: Runge-Kutta 5  
  typedef runge_kutta_dopri5< std::vector<AD<double>> > stepper_type;  
  stepper_type stepper;  
    
  // Observer  
  std::vector< std::vector<AD<double>> > states;  
  std::vector<double> time_points;  
  push_back_state_and_time< std::vector<AD<double>> > observer(states, time_points);  
    
  // Time steps  
  std::vector<double> tvec = as< std::vector<double> >(times);  
    
  vdp_system sys(mu);  
    
  for(size_t i=1; i<tvec.size(); ++i) {  
    double t = tvec[i-1];  
    double dt = tvec[i] - tvec[i-1];  
    stepper.do_step(sys, x, t, dt);  
    observer(x, t+dt);  
  }  
    
  // Flatten output for CppAD function  
  std::vector< AD<double> > Y_flat;  
  for(auto &s : states) {  
    Y_flat.push_back(s[0]); // x  
    Y_flat.push_back(s[1]); // v  
  }  
    
  // CppAD function  
  CppAD::ADFun<double> f(X, Y_flat);  
    
  // Jacobian: 2*n_steps x 3  
  std::vector<double> X_in = {x0_val, v0_val, mu_val};  
  std::vector<double> jac = f.Jacobian(X_in);  
    
  // Output matrix: 9 columns  
  int n = states.size();  
  NumericMatrix result(n, 9);  
  colnames(result) = CharacterVector::create(  
    "time","x","v","x.mu","v.mu","x.x","v.x","x.v","v.v"  
  );  
    
  const int num_params = 3;  
    
  for(int i=0; i<n; ++i) {  
    result(i,0) = time_points[i];  
    result(i,1) = CppAD::Value(states[i][0]);  
    result(i,2) = CppAD::Value(states[i][1]);  
      
    // Sensitivities from Jacobian (row-major: rows are outputs, columns are parameters)  
    int row_x = 2 * i;       // Output index for x at time step i  
    int row_v = 2 * i + 1;   // Output index for v at time step i  
      
    // Columns: x.mu (dx/dmu), v.mu (dv/dmu)  
    result(i,3) = jac[row_x * num_params + 2];  
    result(i,4) = jac[row_v * num_params + 2];  
      
    // Columns: x.x (dx/dx0), v.x (dv/dx0)  
    result(i,5) = jac[row_x * num_params + 0];  
    result(i,6) = jac[row_v * num_params + 0];  
      
    // Columns: x.v (dx/dv0), v.v (dv/dv0)  
    result(i,7) = jac[row_x * num_params + 1];  
    result(i,8) = jac[row_v * num_params + 1];  
  }  
    
  return result;  
}  
```  
From R, you can then run:  
```R  
Rcpp::sourceCpp("integrate_vdp_sens.cpp")  
  
params <- c(mu = 2, x = 2, v = 0)  
times <- seq(0, 10, length.out = 300)  
res_CppAD <- integrate_vdp_rk_sens(times, params)  
```  
  
I’m also working on examples for controlled Runge-Kutta steppers as well as stiff solvers, such as `rosenbrock4`. I’m still facing some difficulties with properly implementing state types, algebras, and operations, which need to be defined correctly for CppAD to work seamlessly with Boost.
