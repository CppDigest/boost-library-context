# #491 - Tests fail after the addition of new pow function in boost rational [Closed]

> Username: vissarion  
> Created at: 2018-06-20 08:14:15 UTC  
> Updated at: 2018-06-21 12:30:36 UTC  
> Closed at: 2018-06-21 12:30:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/491  

After this addition https://github.com/boostorg/rational/commit/25ad8e5a5cc382c483dde8d6c019ba970b443f62 some tests in area and projections fail.  
  
The errors look like this   
`../../boost/geometry/formulas/area_formulas.hpp:332:59: note:   mismatched types ‘boost::rational<Integer>’ and ‘double’  
             coeffs2[i] = ((i==0) ? CT(1) : pow(var2,int(i)))`
