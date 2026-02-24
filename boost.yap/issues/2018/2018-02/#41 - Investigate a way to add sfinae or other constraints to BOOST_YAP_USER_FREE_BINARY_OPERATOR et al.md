# #41 - Investigate a way to add sfinae or other constraints to BOOST_YAP_USER_FREE_BINARY_OPERATOR et al [Closed]

> Username: tzlaine  
> Created at: 2018-02-16 00:35:54 UTC  
> Updated at: 2018-06-11 17:16:19 UTC  
> Closed at: 2018-03-04 02:34:31 UTC  
> Url: https://github.com/boostorg/yap/issues/41  

>>>> You could also get rid of the need for `::user_expr`  
>>>  
>>>  
>>> We actually want that.  It's often necessary to be able to return an  
>>> expression created from some template different than the template  
>> defining  
>>> he operator.  
>>>  
>>  
>>   If that's really important then why doesn't  
>> BOOST_YAP_USER_FREE_BINARY_OPERATOR take two  
>> expr_template parameters?  
>  
>  
> Because there's only one result.   The expr_template parameter governs how  
> the result is constructed, not which inputs are accepted.  
>  
  
  Doesn't this make BOOST_YAP_USER_FREE_BINARY_OPERATOR  
unusable, since expression already uses it, so using it  
again with a different ExpressionTemplate would be ambiguous?  
  
  
Sure, if you use both of them.  I expect people only to use expression for quick-and-dirty small-scale uses of Yap, or for experimentation though.  
  
However, the same problem exists if I have ET1 and ET2 that want to use  BOOST_YAP_USER_FREE_BINARY_OPERATOR.  In that case, the same ambiguity exists.  The solution is the same as any operator overload ambiguity, I think: sfinae.  If there's not already an appropriate constraining macro to replace BOOST_YAP_USER_FREE_BINARY_OPERATOR (I'd have to look around a bit), I should add one. *TODO*

---

## Comment 1

> Username: tzlaine  
> Created at: 2018-03-04 02:34:31 UTC  
> Url: https://github.com/boostorg/yap/issues/41#issuecomment-370196851  

The particular problem outlined above is disproved by the recent move to free operator overloads.  There is not conflict with yap::expression.
