# #39 - one/quantity support [Open]

> Username: ccorn  
> Created at: 2019-08-06 17:07:14 UTC  
> Updated at: 2019-08-07 01:10:37 UTC  
> Url: https://github.com/boostorg/units/issues/39  

While working on an adaptation of a generic vector class to support `boost::units`, I found `boost::units::one` to be quite helpful. It can act as a substitute for a `unit_type` when there is no quantity (and no system of units) involved, e.g. just a built-in floating-point type.  
  
Using `one` got me quite far until I tried to prepare for instances of units, or `one`, divided by actual values. This brings `divide_typeof_helper<one, T>::type` into play. Which turns out to be `T`, even if `T` is a unit or a quantity type. (Such specializations are given in `detail/one.hpp` and `quantity.hpp`.) The expected result would be something with the reciprocated unit type, i.e. all base unit exponents sign-flipped.  
  
I have looked for effects on officially documented functions, including `pow<-1>` (since `static_int_power_sign_impl<N, true>` literally uses `divide_typeof_helper<one,`), but I have found no misbehavior on the surface. Somehow the implementation manages to avoid this case, so I cannot label this as a bug.  
  
Nevertheless, if you want `boost::units::one() / x` to work as intended for a quantity or unit constant `x`, you can use the attached [boost-units-one-div.cc.txt](https://github.com/boostorg/units/files/3473295/boost-units-one-div.cc.txt) as a basis for a test case.

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-08-06 23:31:55 UTC  
> Url: https://github.com/boostorg/units/issues/39#issuecomment-518881125  

AMDG  
  
`one` was never intended to be a public API.  It exists  
to simplify several parts of the implementation and was  
only designed to work with scalars.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: correaa  
> Created at: 2019-08-07 01:10:37 UTC  
> Url: https://github.com/boostorg/units/issues/39#issuecomment-518899369  

@ccorn, I personally define a constant called "uno" (symbol "U") https://iopscience.iop.org/article/10.1088/0026-1394/35/6/3  
Would that work for you?  
  
https://gitlab.com/correaa/boost-units/blob/master/systems/si/abbreviations.hpp
