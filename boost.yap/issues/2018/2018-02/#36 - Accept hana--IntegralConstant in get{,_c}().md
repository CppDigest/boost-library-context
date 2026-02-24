# #36 - Accept hana::IntegralConstant in get{,_c}() [Closed]

> Username: tzlaine  
> Created at: 2018-02-15 20:48:02 UTC  
> Updated at: 2018-06-11 17:16:19 UTC  
> Closed at: 2018-02-20 07:04:23 UTC  
> Url: https://github.com/boostorg/yap/issues/36  

>>>> 221: template <long long I, typename Expr>  
>>>>      decltype(auto) get (Expr && expr, hana::llong<I> i);  
>>>>    Does this need to be restricted to hana::llong, rather  
>>>>    than, say, std::integral_constant?  
>>>>  
>>>  
>>> It's the lack of nice syntax for integral_constant literals that made me  
>>> choose this.  I write get(expr, N_c) a lot, and I expect users to as  
>> well.  
>>>  
>>  
>>   Supporting an IntegralConstant doesn't mean that  
>> you can't pass 0_c.  
>  
>  
> Is the change from std::integral_constant to IntegralConstant significant?  
> That is, are you asking that I accept models of some concept, or just  
> std::integral_constant?  
>  
  
  I meant hana::IntegralConstant in the first place.  
std::integral_constant was just an example of another  
IntegralConstant, which hana::at can accept.
