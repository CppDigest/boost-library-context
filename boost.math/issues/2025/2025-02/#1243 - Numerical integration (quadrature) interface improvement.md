# #1243 - Numerical integration (quadrature) interface improvement [Closed]

> Username: matwey  
> Created at: 2025-02-01 08:27:00 UTC  
> Updated at: 2025-02-01 08:37:59 UTC  
> Closed at: 2025-02-01 08:37:59 UTC  
> Url: https://github.com/boostorg/math/issues/1243  

I would like to note that current boost.math can be further improved. The issue is the following. Currently, integrator invokes the user provided function per each point. This approach has been used in numerical integration software since ages. However many quadratures (i.e. Double exponential or Gauss) have precomputed abscissas of interest and there is a plain for-loop inside of such integrator.  
  
Currently, I am developing a software performing scientific calculation. And there is a Python interface for my software, like for many other modern scientific software. My issue is that part of the integrand function is provided by Python user, and I've found that calling Python function from C++ side is very expensive operation (way more expensive than all integrand calculating). In Python world there is a common pattern for such issue: function should accept `numpy`-like array as an argument.  
  
If I had been able to obtain all currently known abscissas, then all required values would have been calculated at a time and integrand function call number would have been reduced to a small number.
