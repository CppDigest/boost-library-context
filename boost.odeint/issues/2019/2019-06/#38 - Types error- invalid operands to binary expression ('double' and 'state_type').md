# #38 - Types error: invalid operands to binary expression ('double' and 'state_type') [Open]

> Username: msurov  
> Created at: 2019-06-06 14:41:55 UTC  
> Updated at: 2019-06-15 03:57:16 UTC  
> Url: https://github.com/boostorg/odeint/issues/38  

I defined new state type as  
```c++  
struct state_type { double x; };  
```  
and vector space operators  
```c++  
state_type operator * (const state_type &a, double k) { return {a.x * k}; }  
state_type operator * (double k, const state_type &a) { return {a.x * k}; }  
state_type operator + (const state_type &a, const state_type &b) { return {a.x + b.x}; }  
state_type operator - (const state_type &a, const state_type &b) { return {a.x - b.x}; }  
state_type operator / (const state_type &a, const state_type &b) { return {a.x / b.x}; }  
state_type abs(state_type const& v) { return {fabs(v.x)}; }  
```  
The main function is   
```c++  
int main()  
{  
    state_type x0 {1.};  
    double t0 = 0, t1 = 1, dt = 1e-3;  
    using Stepper = runge_kutta_dopri5<state_type, double, state_type, double, vector_space_algebra>;  
    auto f = [](state_type const& x, state_type& dx, double t) {  
        dx = x;  
    };  
    integrate_adaptive(make_controlled<Stepper>(1e-10, 1e-10), f, x0, t0, t1, dt);  
}  
```  
During compilation I am getting the error  
  
> /usr/include/boost/numeric/odeint/algebra/default_operations.hpp:443:76: error: invalid operands to binary expression ('double' and 'state_type')  
> [build]             set_unit_value( t3 , abs( get_unit_value( t3 ) ) / ( m_eps_abs + m_eps_rel * ( m_a_x * abs( get_unit_value( t1 ) ) + m_a_dxdt * abs( get_unit_value( t2 ) ) ) ) );  
>   
If I add the requested operator  
```c++  
state_type operator + (double k, const state_type &a) { return {a.x + k}; }  
```  
the compilation works well. It seems that the formula   
```c++  
set_unit_value( t3 , abs( get_unit_value( t3 ) ) / ( m_eps_abs + m_eps_rel * ( m_a_x * abs( get_unit_value( t1 ) ) + m_a_dxdt * abs( get_unit_value( t2 ) ) ) ) );  
```  
is wrong, or type of `m_eps_abs` is wrong. In a vector space there must not be operator `+` between elements of state_type and and coefficients. Moreover the operator `/` between state_type elements is questionable.

---

## Comment 1

> Username: ddemidov  
> Created at: 2019-06-07 09:45:08 UTC  
> Url: https://github.com/boostorg/odeint/issues/38#issuecomment-499824841  

`abs(state_type)` should return `double`, but returns `state_type`.

---

## Comment 2

> Username: msurov  
> Created at: 2019-06-14 21:15:38 UTC  
> Url: https://github.com/boostorg/odeint/issues/38#issuecomment-502265743  

In lorenz_point.cpp example the return type is `state_type`:  
```c++  
point3D abs( const point3D &p )  
{  
    return point3D( std::abs(p.x) , std::abs(p.y) , std::abs(p.z) );  
}  
```

---

## Comment 3

> Username: ddemidov  
> Created at: 2019-06-15 03:57:16 UTC  
> Url: https://github.com/boostorg/odeint/issues/38#issuecomment-502332106  

They do define division between `state_types`. Note the comment here:  
  
https://github.com/boostorg/odeint/blob/2bbc186b43d7267e622c4e1ad9527e4a8ecc8c42/examples/lorenz_point.cpp#L57-L68  
  
Addition between `state_type` and `double` works because there is an implicit conversion from `double` to `state_type`:  
  
https://github.com/boostorg/odeint/blob/2bbc186b43d7267e622c4e1ad9527e4a8ecc8c42/examples/lorenz_point.cpp#L34-L36  
  
Not sure if that is intended. May be @headmyshoulder or @mariomulansky can comment.
