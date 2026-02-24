# #1208 - Review 2: Literals docs [Closed]

> Username: jzmaddock  
> Created at: 2025-11-05 18:44:42 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-12 18:21:36 UTC  
> Url: https://github.com/boostorg/decimal/issues/1208  

>The text I would add would be something like: "This example demonstrates decimal  
floating point (user defined) literals, `numeric_limits` for DFP, as well as  
some of the constants made available by the library."  
  
>Please consider an example that does not need the `float_equal` function for  
the reasons described in the next subsection.  
  
Comment: this ties in with the need to describe and hand hold through the examples a bit more.  
  
The `float_equal` function is very badly named IMO: it checks for low absolute error, which may or may not be what is required here.  At the very least perhaps rename to something like `has_low_absolute_error` and add a comment about it being simplified, and/or for exposition only.  I get that we don't want a large chunk of subtle code here.  
  
In fact if we look at the checks:  
  
```  
assert(float_equal(pi_32, static_cast<decimal32_t>(pi_64))); // Explicit conversion between decimal types  
```  
  
Are there not enough extra digits in a decimal64_t that it should round to exactly the correct value?  Might be a good example of rounding actually.  
  
```  
    assert(float_equal(pi_32, boost::decimal::numbers::pi_v<decimal32_t>)); // Constants available in numbers namespace  
```  
  
Surely these are the same type and should have exactly the same value?  
  
```  
    assert(float_equal(pi_64, numbers::pi)); // Default constant type is decimal64_t  
```  
Again, the same types, and should be exactly equal?

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-06 07:58:58 UTC  
> Url: https://github.com/boostorg/decimal/issues/1208#issuecomment-3495670663  

I was hoping that people would recognize that was not a true float equal, but Knuth's approximate float equal algorithm. I'm thinking of putting together a trivial test harness to replace the asserts and all. I don't want to use Boost.Test or Boost.core.lightweight test in the examples because the lib can be used just by cloning this repo.
