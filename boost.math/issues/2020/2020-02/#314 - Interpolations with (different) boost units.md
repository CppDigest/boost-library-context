# #314 - Interpolations with (different) boost units [Open]

> Username: sebweb3r  
> Created at: 2020-02-07 12:45:19 UTC  
> Updated at: 2020-07-07 09:19:41 UTC  
> Url: https://github.com/boostorg/math/issues/314  

Hey,   
  
we (a physics group) are using the boost math (Barycentric Rational Interpolation and others)   
with boost units for our code. We have the problem, that the interpolation expects the same   
type for x and y data. In physics we quiet often have different units.  
  
Could you extend the templates for different types/units for x and y.  
  
Thanks in advance.

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-02-07 13:37:29 UTC  
> Url: https://github.com/boostorg/math/issues/314#issuecomment-583392195  

Yes, I think that's fine. Could you give me a main I could try to get working?  
  
BTW, I recommend pchip or makima interpolation over barycentric rational for almost all uses (log(N) complexity rather than O(N), with less spurious oscillations).

---

## Comment 2

> Username: L0ric0  
> Created at: 2020-07-07 09:19:41 UTC  
> Url: https://github.com/boostorg/math/issues/314#issuecomment-654718765  

i think something like this would be nice: https://github.com/L0ric0/boostorg-mat-issue-314  
  
it could be that i was wrong with the template arguments for ` boost::math::barycentric_rational<quantity<si::energy, double>, quantity<si::electric_charge, double>>` but they should be the types of the axes for the function to interpolate.
