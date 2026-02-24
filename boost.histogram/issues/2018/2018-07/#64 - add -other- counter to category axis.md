# #64 - add "other" counter to category axis [Closed]

> Username: HDembinski  
> Created at: 2018-07-18 11:33:10 UTC  
> Updated at: 2018-07-22 20:56:20 UTC  
> Closed at: 2018-07-22 20:56:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/64  

The current system allows axes with or without under- and overflow bins. There is no way to make an axis which only has overflow, but not underflow, but this seems possible with a small change. Once this has been implemented, it should be used in the category axis to have a counter for "other" items which do not fall into one of the recognized categories. It would also be useful for a regular axis with purely non-negative values, like radii. There is no need for an underflow bin, but an overflow bin is useful.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-07-22 20:56:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/64#issuecomment-406896103  

Implemented in develop
