# #42 - controlled_runge_kutta complex State error with Eigen. [Open]

> Username: Mephphisto  
> Created at: 2020-08-10 12:08:47 UTC  
> Updated at: 2020-11-11 09:26:24 UTC  
> Url: https://github.com/boostorg/odeint/issues/42  

I get an error in Line 89:  
`return algebra.norm_inf( x_err );`   cant convert std::complex<double> to double   
i attempted to work around this by inserted a abs()   
`return abs(algebra.norm_inf( x_err )); `  
is this correct?  
  
My state type is Eigen::VectorXcd   
my Value type is double.  
I'm  using   
`#include <boost/numeric/odeint/external/eigen/eigen.hpp>`  
  
Thank you in advance and best Regards!

---

## Comment 1

> Username: tueda  
> Created at: 2020-11-11 09:26:23 UTC  
> Url: https://github.com/boostorg/odeint/issues/42#issuecomment-725310280  

I think your workaround correctly works (with a negligible computational cost, though).  
  
It seems that the problem originates in  
https://github.com/boostorg/odeint/blob/2bbc186b43d7267e622c4e1ad9527e4a8ecc8c42/include/boost/numeric/odeint/external/eigen/eigen_algebra.hpp#L89  
which defines the result type of the `norm_inf()`. Here `B` is the scalar type (which is `std::complex<double>` in your case), though `lpNorm()` of Eigen returns a real value (`double`), so the result type should be just `double`.
