# #47 - fail to compile on signed vs. unsigned comparison [Closed]

> Username: akrzemi1  
> Created at: 2017-03-17 22:55:13 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2018-08-10 20:44:25 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/47  

This is just a suggestion.  
Currently the following returns true:   
  
```  
safe<signed>{-1} < safe<unsigned>{1}  
```  
  
Which is quite clever a fix, but maybe instead, this should fail to compile, and therefore force the user to rethink the comparison and do one of the following:  
  
1. Change the signed-ness of one of the types  
2. Use a named function for comparison.

---

## Comment 1

> Username: robertramey  
> Created at: 2018-08-10 20:44:25 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/47#issuecomment-412200811  

I went through and more carefully follow the language / procedures of the standard.  
  
The standard considers < (and other operators) binary operators.  
In such case, promotion rules are invoked to promote both operands to the same type.  
then the types are converted.  
  
depending on the size of the two operands, the promotion may result in an (undetected) error.  This can occur when a negative value is promoted to an unsigned value - which can happen.  On cases like this, errors injected during promotions are caught as errors.  This means that we aren't changing C++/C behavior, we are just trapping the places where it occurs.  This is in the spirit of the library.
