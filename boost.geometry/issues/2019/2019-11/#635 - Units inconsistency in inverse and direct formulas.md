# #635 - Units inconsistency in inverse and direct formulas [Closed]

> Username: awulkiew  
> Created at: 2019-11-02 13:55:54 UTC  
> Updated at: 2022-11-22 15:06:05 UTC  
> Closed at: 2022-11-22 15:06:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/635  

Recently added Karney's formulas take and return coordinates and azimuth in degrees while other formulas take them and return them in radians. This behavior was inherited from the original implementation of GeographicLib so it's fine. However this means that currently Karney's formulas can't be used in the library in a generic way. We either has to use units consistently across all formulas and loose the properties of the Karney's formula or add a level of abstraction between formulas and strategies. Or change interface of all formulas.  
  
First of all I think we should keep the degrees because AFAIU the reason is to allow to express 180 angle precisely. Or is there some other reason to keep degrees?  
  
This leaves the two other options. I'm considering passing the information about units with angle, just like we pass information about units with points. So have a type like `angle<degree>`. This would allow us to clearly represent azimuths and instead of raw angles use points and unit-aware-angles in formulas or in a layer above. This would also allow us to return them e.g. from `azimuth` and therefore to give the user the control over the unit of the result.  
  
What do you think?

---

## Comment 1

> Username: vissarion  
> Created at: 2019-11-04 09:39:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/635#issuecomment-549278416  

I am ok with the intermediate level you are proposing, but I would also be ok with simply transform "karney" formulas to have the same interface as all the other inverse formulas, *i.e.*, add a radian to degree in the beginning and a degree to radian just before returning.

---

## Comment 2

> Username: vissarion  
> Created at: 2022-11-22 15:06:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/635#issuecomment-1323821904  

This is now fixed. See https://github.com/boostorg/geometry/pull/1087
