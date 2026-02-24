# #50 Fix boost::math::polynomial::evaluate for the zero polynomial [Merged]

> Username: kartikmohta  
> Created at: 2016-10-17 20:21:57 UTC  
> Updated at: 2016-10-25 07:24:27 UTC  
> Merged at: 2016-10-19 18:33:24 UTC  
> Closed at: 2016-10-19 18:33:24 UTC  
> Url: https://github.com/boostorg/math/pull/50  

Due to a recent change in the representation of the zero polynomial (739c056f2b17c047cbeaed9a083275a7aff017a1), I think the evaluate function is broken for the zero polynomial.  
  
Currently if the evaluate method is called on a zero polynomial, it returns:  
  
```  
/usr/include/boost/math/tools/rational.hpp:191: U boost::math::tools::evaluate_polynomial(const T*, const U&, std::size_t) [with T = double; U = double; std::size_t = long unsigned int]: Assertion `count > 0' failed.  
```  
  
I opened a ticket regarding the same at https://svn.boost.org/trac/boost/ticket/12532 since I was not sure which is the preferred medium.

---
