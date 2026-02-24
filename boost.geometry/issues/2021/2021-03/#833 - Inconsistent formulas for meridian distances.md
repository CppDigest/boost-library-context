# #833 - Inconsistent formulas for meridian distances [Open]

> Username: cffk  
> Created at: 2021-03-24 18:59:05 UTC  
> Updated at: 2022-10-19 12:18:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/833  

It would be nice if `quarter_meridian`, `meridian_direct`, and `meridian_inverse` all used consistent approximations.  Instead:  
* `quarter_meridian` is accurate to `n^16` with the coefficient given to full precision.  
* `meridian_direct` is accurate to `n^4` with the coefficients given approximately, e.g., `1.572916667` instead of `CT(151)/CT(96)`.  
* `meridian_inverse` is supposed to be accurate to `n^5`, however the `n^4` contribution to `C0` and the `n^5` contribution to `C2` are omitted and the coefficients are given approximately, e.g. `-0.729166667` instead of `CT(-35)/CT(48)`.  
  
I recommend that both `median_inverse` and `meridian_direct` include terms up to `n^6` which is needed to full double precision accuracy for the earth.  If these routines used Horner and Clenshaw summation then there would be little cost in making `n^6` the minimum order provided.
