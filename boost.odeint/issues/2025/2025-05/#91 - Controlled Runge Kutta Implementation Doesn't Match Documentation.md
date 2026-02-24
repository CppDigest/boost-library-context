# #91 - Controlled Runge Kutta Implementation Doesn't Match Documentation [Open]

> Username: mczek  
> Created at: 2025-05-29 13:50:37 UTC  
> Updated at: 2025-05-29 13:50:37 UTC  
> Url: https://github.com/boostorg/odeint/issues/91  

In the case where the time step is increased the function `increase_step` is used from `include/boost/numeric/odeint/stepper/controlled_runge_kutta.hpp` which I've copied below:  
```  
    time_type increase_step(time_type dt, value_type error, const int stepper_order) const  
    {  
        // returns the increased time step  
        BOOST_USING_STD_MIN();  
        BOOST_USING_STD_MAX();  
        using std::pow;  
  
        // adjust the size if dt is smaller than max_dt (providede max_dt is not zero)  
        if(error < 0.5)  
        {  
            // error should be > 0  
            error = max BOOST_PREVENT_MACRO_SUBSTITUTION (  
                    static_cast<value_type>( pow( static_cast<value_type>(5.0) , -static_cast<value_type>(stepper_order) ) ) ,  
                    error);  
            // time_type dt_old = dt;   unused variable warning   
            //error too small - increase dt and keep the evolution and limit scaling factor to 5.0  
            dt *= static_cast<value_type>(9)/static_cast<value_type>(10) *  
                  pow(error, static_cast<value_type>(-1) / stepper_order);  
            if(m_max_dt != static_cast<time_type >(0))  
                // limit to maximal stepsize  
                dt = detail::min_abs(dt, m_max_dt);  
        }  
        return dt;  
    }  
```  
  
Inside of the control flow, $error = \max(5^{-S}, error)$ and then dt is multiplied by $0.9 * error^{-1/S} = 0.9*\min(5, error^{-1/S})$ which limits the maximum step size change to a factor of 4.5.  
  
The documentation [here](https://live.boost.org/doc/libs/1_88_0/libs/numeric/odeint/doc/html/boost_numeric_odeint/odeint_in_detail/steppers.html) states that `if val < 0.5 : dt​_new = dt​_current min( 0.9 pow( val , -1 / O​S ) , 5 )` which has the factor of 0.9 inside the minimum. In this case the maximum step size change is instead limited to a factor of 5.   
  
I'll offer to attempt a PR for the documentation if someone confirms my understanding is correct.
