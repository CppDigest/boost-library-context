# #41 - infinite loop in integrate_adaptive/odeint [Open]

> Username: moribana  
> Created at: 2019-12-13 13:58:29 UTC  
> Updated at: 2019-12-13 13:58:29 UTC  
> Url: https://github.com/boostorg/odeint/issues/41  

I am running into an infinite loops applying integrate_adaptive in the dense output version (odeint/integrate/detail/integrate_adaptive.hpp l. 122).  
  
That's an easy test case replicating the issue:  
  
```  
auto f = []( const double& /*y*/, double& dy_dx, double /*x*/ ) {  
        dy_dx = haas::math::numeric::constant<double>::two_pi() / -35.0;  
    };  
    typedef boost::numeric::odeint::runge_kutta_dopri5<state_type> error_stepper_type;  
    auto stepper = boost::numeric::odeint::make_dense_output<error_stepper_type>( 1.0e-6, 1.0e-5);  
    // Initial conditions  
    state_type state1 = 0.0;  
    // Solve ODE  
    boost::numeric::odeint::integrate_adaptive( stepper, f, state1, 0.0, -23.8, -23.8 / 100 );  
```  
  
  
The problem seems to rise from a mismatch between the new initialization of  stepper st ( l. 144) and the while loop condition (l. 135).  
  
I would suggest to change l. 135-137 from  
```  
        while( less_eq_with_sign( static_cast<Time>(st.current_time() + st.current_time_step()) ,  
               end_time ,  
               st.current_time_step() ) )  
```  
into:   
```  
                    while( less_eq_with_sign(  
                        st.current_time_step(), static_cast<Time>( end_time - st.current_time() ),  
                        st.current_time_step() ) )  
```  
  
Comments very welcome!
