# #60 - Clarify transform() requirements [Closed]

> Username: tzlaine  
> Created at: 2018-02-21 05:55:37 UTC  
> Updated at: 2018-06-11 17:16:20 UTC  
> Closed at: 2018-02-25 21:00:12 UTC  
> Url: https://github.com/boostorg/yap/issues/60  

>> - How useful is it to separate the Expression concept  
>>   from the ExpressionTemplate concept?  
>  
>  
> Types and templates are not the same kind of entity.  I don't know how to  
> even go about combining these.  Moreover, I think they should remain  
> separated, because sometimes a function takes an Expression, sometimes not,  
> sometimes it requires an ExpressionTemplate template parameter, and  
> sometimes not.  These are orthogonal requirements for the caller, no?  
>  
>  
>> For example,  
>>   transform requires an ExpressionTemplate for nodes  
>>   that are not handled explicitly, but that isn't very  
>>   clear from the documentation, which just says that  
>>   it takes an Expression.  
>>  
>  
> I don't understand what you mean.  transform() does not require an  
> extrinsic ExpressionTemplate template parameter, and does not use one.  It  
> just recycles the ExpressionTemplate that was originally used to  
> instantiate whatever it needs to copy.  
>  
  
Let me rephrase the question:  
Is it useful to allow Expressions that are not  
instantiations of an ExpressionTemplate, given  
that transform can choke on such classes.  
  
Hm.  I think this is a documentation failure.  I need to look and see what the actual requirements are.  I think it should always be fine to have non-template-derived Expressions in terminals.  This is where one usually writes them anyway.
