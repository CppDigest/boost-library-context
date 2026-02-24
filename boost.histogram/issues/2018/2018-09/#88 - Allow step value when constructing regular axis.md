# #88 - Allow step value when constructing regular axis [Closed]

> Username: uecasm  
> Created at: 2018-09-18 08:04:17 UTC  
> Updated at: 2019-01-19 12:21:49 UTC  
> Closed at: 2019-01-19 12:21:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/88  

Provided that it doesn't negatively impact performance, I think it'd be nice if `axis::regular` offered an additional constructor which takes `value_type step` (or `delta`, since you're already using that name internally) instead of `unsigned n`.  
  
It would then calculate n internally as `unsigned((trans.forward(upper) - trans.forward(lower)) / trans.forward(step))` (or equivalent).  
  
This is a usability convenience; I think that at least in some cases it's more natural to think of an interval and the size of the step for each bin, with less concern for the number of bins thus required.  (This can later be discovered via `size()` or iteration.)  
  
One downside of this is that for non-integer value types it would be possible to pass parameters where the upper value is not met (when the interval divided by the step has some remainder).  But that's just a matter of defining the intended behaviour for this case -- since the upper is open anyway I think it makes the most sense to truncate `n` (as above), such that an input value of exactly `upper` is always guaranteed to overflow.  
  
Another downside is that it assumes that conversion from the result of the `value_type` division to `unsigned` is possible, which is true for the built-in integer and floating types but might not be true for a custom value type.  But perhaps that constructor could simply be disabled in that case?

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-09-18 08:15:59 UTC  
> Updated at: 2018-09-18 08:17:13 UTC  
> Url: https://github.com/boostorg/histogram/issues/88#issuecomment-422299578  

Dear Gavin, thanks this makes sense to me.

---

## Comment 2

> Username: jpivarski  
> Created at: 2018-09-22 11:40:09 UTC  
> Url: https://github.com/boostorg/histogram/issues/88#issuecomment-423738279  

This is similar to the distinction between Numpy's `arange` (defined by a lower edge and a step size, stops at or before an upper edge) and `linspace` (defined by lower and upper edges and an exact number of bins). Perhaps your histogram bins should have the same semantics and edge cases.

---

## Comment 3

> Username: HDembinski  
> Created at: 2018-09-24 07:32:35 UTC  
> Url: https://github.com/boostorg/histogram/issues/88#issuecomment-423894898  

Agreed, I was thinking the same.

---

## Comment 4

> Username: HDembinski  
> Created at: 2018-11-19 12:42:50 UTC  
> Updated at: 2018-11-19 12:44:13 UTC  
> Url: https://github.com/boostorg/histogram/issues/88#issuecomment-439880370  

@uecasm I would like to implement this now, but I am wondering about the design of the constructor signature. Technically, the two cases have distinct signatures. The original one accepts an integer for the number of bins, while the new ctor would accept a floating point number for the delta. Nevertheless, both are very similar and in real-life, there will be errors such as this  
```  
axis::regular<>(10, 0, 10); // ok: 10 bins from 0 to 10 with 1 spacing  
axis::regular<>(1, 0, 10); //  not ok: 10 bins from 0 to 10 with 1 spacing intended, but this creates an axis with 1 bin from 0 to 10  
```  
So to make this case less ambiguous, I think I should introduce a helper function (as I do already in other parts of the library), to make the second call look like so:  
```  
axis::regular<>(step(1), 0, 10); // ok, 10 bins 0 to 10 with 1 spacing  
```  
`step` is a function that returns a special `step_type`, which is not convertible to floating point and makes sure that the two ctors are not accidentally mixed up and the intend in the user code is clearly expressed.

---

## Comment 5

> Username: HDembinski  
> Created at: 2018-11-19 12:47:16 UTC  
> Url: https://github.com/boostorg/histogram/issues/88#issuecomment-439881518  

`step` cannot just be a function that does a `static_cast` to double, because we don't know whether the external value type of `axis::regular` is a floating point number. It could be `quantity` from the Boost.Units library, and then casting to double wouldn't work.

---

## Comment 6

> Username: HDembinski  
> Created at: 2018-11-19 12:49:07 UTC  
> Url: https://github.com/boostorg/histogram/issues/88#issuecomment-439882013  

The alternative would be a factory function which creates an `axis::regular` from a step size. I am not sure whether that is better, I am leaning towards the approach presented here.

---

## Comment 7

> Username: uecasm  
> Created at: 2018-11-19 20:48:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/88#issuecomment-440036672  

The call syntax is fine that way (and makes the signature a bit clearer, which is a good thing), although as `step_type` would have to be templated on `value_type` it complicates the internals a bit.

---

## Comment 8

> Username: HDembinski  
> Created at: 2018-11-20 11:07:12 UTC  
> Url: https://github.com/boostorg/histogram/issues/88#issuecomment-440235232  

> although as step_type would have to be templated on value_type it complicates the internals a bit.  
  
Right, I am doing this already in a few places. Considering the overall complexity that this library has already accumulated, it is not an issue.
