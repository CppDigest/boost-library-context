# #3 - unused parameter warning in zero_vec [Closed]

> Username: federkamm  
> Created at: 2016-11-03 12:22:47 UTC  
> Updated at: 2016-11-04 03:38:13 UTC  
> Closed at: 2016-11-04 03:38:13 UTC  
> Url: https://github.com/boostorg/qvm/issues/3  

boost 1.62.0: vec_operations.hpp: line 258 and others cause "unused parameter warnings".  The code reads roughly  
  
    template <int I> scalar_type read_element(this_vector const & x) {  
      return 0;  
    }  
  
where x is unused.  
  
Plese either remove x from the parameter list:  
  
    ... read_element(this_vector const &) { ... }  
  
or declare as unused  
  
    ... read_element( ... ) {  
      (void)x;  
      return 0;  
    }

---

## Comment 1

> Username: zajo  
> Created at: 2016-11-04 03:38:13 UTC  
> Url: https://github.com/boostorg/qvm/issues/3#issuecomment-258337779  

Thanks -- done, see latest develop branch.
