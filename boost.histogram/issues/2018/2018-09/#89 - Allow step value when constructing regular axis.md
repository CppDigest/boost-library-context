# #89 - Allow step value when constructing regular axis [Closed]

> Username: HDembinski  
> Created at: 2018-09-18 08:07:07 UTC  
> Updated at: 2018-09-18 08:18:22 UTC  
> Closed at: 2018-09-18 08:18:21 UTC  
> Url: https://github.com/boostorg/histogram/issues/89  

Gavin Lambert  
> I think it'd be nice if axis::regular offered an additional constructor which takes value_type step (or delta, since you're already using that name internally) instead of unsigned n.  
>  
> It would then calculate n internally as unsigned((trans.forward(upper) - trans.forward(lower)) / trans.forward(step)) (or equivalent).  
>  
> One downside of this is that for non-integer value types it would be possible to pass parameters where the upper value is not met (when the interval divided by the step has some remainder). But that's just a matter of defining the intended behaviour for this case -- since the upper is open anyway I think it makes the most sense to truncate n (as above), such that an input value of exactly upper is always guaranteed to overflow.  
>  
> Another downside is that it assumes that conversion from the result of the value_type division to unsigned is possible, which is true for the built-in integer and floating types but might not be true for a custom value type. But perhaps that constructor could simply be disabled in that case?

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-09-18 08:18:21 UTC  
> Url: https://github.com/boostorg/histogram/issues/89#issuecomment-422300362  

Duplicate.
