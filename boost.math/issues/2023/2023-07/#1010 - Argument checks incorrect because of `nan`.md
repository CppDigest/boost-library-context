# #1010 - Argument checks incorrect because of `nan`. [Open]

> Username: ryanelandt  
> Created at: 2023-07-31 19:20:40 UTC  
> Updated at: 2023-08-01 17:30:10 UTC  
> Url: https://github.com/boostorg/math/issues/1010  

When checking input arguments, it's common to want `min <= max`. If this statement is not true, the program **needs** to error. One way to make sure the statement is true is to assert that it is true. E.g., with:  
```c++  
BOOST_MATH_ASSERT(min <= max);  
```  
  
A common way of checking the assertion that `min <= max` in the Boost codebase is to use the construction:  
```c++  
if (min > max) {  
      return policies::raise_evaluation_error(function, "assertion min <= max failed (first arg=%1%)", min, boost::math::policies::policy<>());  
}  
```  
  
The first approach has three advantages over the second approach:  
1. It is cleaner and clearer (imo)  
2. It makes disagreement between the error message and performed check impossible (e.g., #1003)  
3. It gives the right answer when one of both arguments is `nan`. E.g.,   
  
```c++  
const auto fn = [](double x) { return std::make_pair(x, 1.0); };   
  
const double inf = std::numeric_limits<double>::infinity();  
const double nan = std::numeric_limits<double>::quiet_NaN();  
     
const double x0 = boost::math::tools::newton_raphson_iterate(fn, -inf,  /* initial guess */  
                                                                  inf,  /* lower bound */  
                                                                  nan,  /* upper bound */  
                                                                  52);  
```  
gives `x0 = nan`, even though the lower bound is `inf`.  
  
**NOTE:**  
The six comparison operators are: `==`, `<`, `<=`, `>`, `>=`, and `!=`. For all comparison operators besides `!=`, if one or both of the arguments are `nan`, the result is `false` according to the IEEE standard.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-08-01 09:27:07 UTC  
> Url: https://github.com/boostorg/math/issues/1010#issuecomment-1659927762  

The issue with assertions is that they are debug only, once the program is built with -D_NDEBUG=1 they disappear.  
  
>The six comparison operators are: ==, <, <=, >, >=, and !=. For all comparison operators besides !=, if one or both of the arguments are nan, the result is false according to the IEEE standard.  
  
True, however it is common for programs to be compiled with options (often the default for release builds) where the NaN's are assumed to simply not exist and these assumptions no longer hold.  Besides which `if(!(min < max))` would have the same logic as your assertion.

---

## Comment 2

> Username: ryanelandt  
> Created at: 2023-08-01 17:30:10 UTC  
> Url: https://github.com/boostorg/math/issues/1010#issuecomment-1660787104  

> Besides which if(!(min < max)) would have the same logic as your assertion.  
  
Yes that's much better. That's what I used in #1003.  
  
> The issue with assertions is that they are debug only, once the program is built with -D_NDEBUG=1 they disappear.  
  
That's true. They also don't print out the values of the things being compared. What's really needed IMO, are macros with the following properties:  
- Don't go away in release mode  
- automatically print the values being compared on failure  
  
So, essentially Boost Test macros, but for runtime use.
