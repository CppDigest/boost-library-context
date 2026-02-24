# #284 Add two-pi and half-pi constants [Merged]

> Username: mkaravel  
> Created at: 2015-04-27 09:53:20 UTC  
> Updated at: 2015-04-28 13:42:57 UTC  
> Merged at: 2015-04-28 12:00:26 UTC  
> Closed at: 2015-04-28 12:00:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/284  

This PR:  
- Adds to more constants in `boost::geometry::math` namespace: `2*π` and `π/2`.  
- `ttmath_big` and `ttmath::Big<Exponent, Mantissa>` are modified to support these two new constants.  
- Strategies that compute these two constants via `math::pi<>()`, now use the corresponding functions directly.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-04-27 10:29:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/284#issuecomment-96599522  

Looks good. How precise should be constants defined for `ttmath::Big`? The user could need something less precise (it could be faster) or more precise than we have. AFAIU all `ttmath::Big` numbers after e.g. multiplying them by `pi` will have the same precision as `pi` if they were less precise. This could kill the performance. Is there an "optimal" precision of `ttmath::Big`?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-04-27 10:52:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/284#issuecomment-96607459  

Good question and this is also related to how we define π, which I have not changed.  
  
I think what happens is that the additional digits provided by our string are discarded, i.e., `ttmath_big` and `ttmath::Big<>` keep only the digits that are necessary to fill the mantissa and throw away the rest.  
`ttmath` numbers are fixed-precision, so the number of bits stored is determined ahead of time and not changed (dynamically or otherwise). In that respect the only overhead that I see in the current implementation is that we provide many more digits than needed for `ttmath_big`, but this overhead is encountered only once when `the_pi` static variable is constructed.  
  
Another solution would be to implement, e.g. `define_pi`, as follows:  
  
```  
template <ttmath::unit Exponent, ttmath::unit Mantissa>  
struct define_pi<ttmath::Big<Exponent, Mantissa>  
{  
    static inline ttmath::Big<Exponent, Mantissa> apply()  
    {  
        ttmath::Big<Exponent, Mantissa> pi_value(0);  
        pi_value.SetPi();  
        return pi_value;  
    }  
};  
```  
  
but this would mean that π would be constructed any time the `apply()` method is called.  
In fact as far as I can understand `SetPi()` does the same as we do: it stores 400 digits ahead of time and uses only those needed to fill the mantissa.  
**Note:** apart from `SetPi()`, `ttmath::Big<>` defines also `Set2Pi()` and `Set05Pi()` for 2*π and π/2, respectively.  
  
I think the current implementation is okay as it is.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-04-28 12:00:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/284#issuecomment-97035387  

All of your points are valid. And I of course like that the values are created only once.

---
