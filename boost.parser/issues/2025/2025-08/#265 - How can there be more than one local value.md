# #265 - How can there be more than one local value? [Closed]

> Username: jwwalker  
> Created at: 2025-08-06 21:05:31 UTC  
> Updated at: 2025-10-13 01:22:34 UTC  
> Closed at: 2025-10-13 01:22:34 UTC  
> Url: https://github.com/boostorg/parser/issues/265  

The tutorial's discussion of `_locals()` says that "If there are two or more local values, `_locals()` returns a reference to a `boost::parser::tuple`."  But how can that happen, given that the declaration of a rule can only provide one local object?  I thought maybe locals could filter down to sub-rules.  That is, if a rule `outer` uses a rule `inner` as part of its definition, I thought maybe a semantic action within `inner` could see the locals of both `inner` and `outer`.  That would be very useful to me if it were true, but it does not seem to be the case.

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-10-13 01:22:34 UTC  
> Url: https://github.com/boostorg/parser/issues/265#issuecomment-3395590761  

There is indeed one template param for locals, but if it is a tuple, that's what you get out of `_locals()`.  The wording there is a little awkward, but that's all I meant.
