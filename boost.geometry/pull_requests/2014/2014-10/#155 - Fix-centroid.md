# #155 Fix/centroid [Merged]

> Username: awulkiew  
> Created at: 2014-10-10 23:01:59 UTC  
> Updated at: 2014-10-31 13:45:55 UTC  
> Merged at: 2014-10-18 15:35:29 UTC  
> Closed at: 2014-10-18 15:35:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/155  

Fix for Geometries containing big coordinates values.  
  
During the calculation of a centroid the strategy may perform calculus using great values, e.g. add squares of coordinates, which greately increases the numerical error. This is related with https://github.com/boostorg/geometry/pull/154.  
  
The main idea is to move the Geometry closer to 0 and calculate the centroid for such Geometry. The new origin is the first Point of a Geometry. The translation is done on-the-fly as centroid is calculated. Then the resulting point is translated back.  
  
The picture below shows the Polygon placed closer to 0 and further, plus their centroids before and after the fix.  
  
![text3152](https://cloud.githubusercontent.com/assets/1226951/4600161/6b8980e8-50cf-11e4-8b71-67eac62defb0.png)

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-10-11 02:33:52 UTC  
> Updated_at: 2014-10-11 03:07:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/155#issuecomment-58735312  

Even though centroid() doesn't support other CS than cartesian because of lack of strategies I prepared the algorithm by disabling translation in other coordinate systems (see the NOTE in the code).  
  
I enabled the translation for all types of geometries, also non-Areal but I'm not sure is this is needed.  
The translation could improve the result in all cases though I'm not sure about it, for non-Areal Geometries only additions and then a division is performed. So the need for transformation probably depends on used Strategy. The transformation could be made a Policy defined by a Strategy or we could just leave it as it is and perform it in all cases. Maybe I should just check if this fix changes/improves the result for e.g. MultiPoint far from 0 containing many Points. If this was the case then I guess we should leave it, if not then think what to do (remove it in algorithm or add a Policy to Strategy). What do you think?  
  
EDIT:  
  
Another solution would be to enclose the translation inside a Strategy, so pass this origin Point into the `Strategy::apply()` and `Strategy::result()` and let the Strategy do whatever it likes with it.  
  
But I'm probably as usual going to much ahead with all of this. I guess we could choose a lazy approach here, or rather change only the broken part. So translate only Areal Geometries inside the algorithm and leave everything else as it was before.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-10-18 10:51:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/155#issuecomment-59607098  

OK to merge this

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-10-18 10:52:13 UTC  
> Updated_at: 2014-10-18 15:34:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/155#discussion_r19052957  

Maybe this can go to another file

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2014-10-18 10:54:52 UTC  
> Updated_at: 2014-10-18 15:34:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/155#discussion_r19052972  

Apparently we have a new convention here, aligning new lines with the brackets before. AFAIR there is nothing in the guidelines about this.  
I'm not against this, but please do not mix this (in the future) with bugfixes or other enhancements. Maybe we should discuss this on the list. If we do this, I'm in favour of putting all parameters separately, one per line

---
