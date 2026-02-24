# #59 Feature/is simple [Merged]

> Username: mkaravel  
> Created at: 2014-06-14 21:15:28 UTC  
> Updated at: 2014-06-16 16:48:50 UTC  
> Merged at: 2014-06-16 16:48:49 UTC  
> Closed at: 2014-06-16 16:48:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/59  

Implement free function is_valid and is_simple for all geometry types.  
- is_valid: returns true if the geometry is valid in the OGC standard sense  
- is_simple: return true if the geometry is simple in the OGC standard sense

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-06-15 18:59:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/59#issuecomment-46124376  

The last three commits above change the default behavior of is_valid for linear geometries: spikes are allowed.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-06-16 00:42:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/59#issuecomment-46133046  

The seven commits above:  
1) fix a bug for is_simple of linear geometries (endpoints points for closed linestrings where handled as boundary points, which is wrong).  
2) re-implement is_simple assuming that input geometries are valid.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-06-16 09:16:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/59#issuecomment-46156831  

The two commits above introduce an additional template parameter for dispatch::is_valid that controls the behavior of is_valid with respect to duplicate points; by default, duplicate points are allowed.

---
