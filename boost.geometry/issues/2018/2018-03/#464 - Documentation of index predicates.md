# #464 - Documentation of index predicates [Closed]

> Username: thk686  
> Created at: 2018-03-12 20:12:11 UTC  
> Updated at: 2018-03-23 10:29:54 UTC  
> Closed at: 2018-03-23 10:29:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/464  

The documentation of index predicates is a bit sparse and rather obtuse. I forked so I could add something a bit more friendly, but I have a rudimentary knowledge of doxygen and don't want to screw up the documentation.  
  
Its takes a bit of careful reading to figure out that contains will return those geometries that contain the passed geometry and within will return those geometries falling within the passed geometry. It seems these have LHS and RHS swapped, which is counter intuitive. The text "returns the indexed geometries that contain the passed geometry" and "returns the indexed geometries falling within the passed geometry" or some such would be really easy to get quickly.  
  
Obviously very minor -- hoping its a helpful suggestion.

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-03-22 23:19:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/464#issuecomment-375489189  

Thanks for the suggestion. Is the following change sufficient?  
https://github.com/boostorg/geometry/commit/b9e33ae18f0beabef646159152aca1670f4ea8af
