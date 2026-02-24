# #183 - It would be nice to have complex rational numbers [Open]

> Username: iolojz  
> Created at: 2020-01-09 13:54:52 UTC  
> Updated at: 2020-01-25 19:20:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/183  

Often enough, one wants to perform exact calculations that do not stay within the real domain. It would be nice if the multiprecision library supported complex numbers with rational coefficients.    
Currently, if I try   
  
`number<complex_adaptor<cpp_rational_backend>>`  
  
I get a compiler error, complaining about a missing typedef `exponent_type`. This seems to indicate that currently a complex number always has real coefficients, which is very unfortunate!
