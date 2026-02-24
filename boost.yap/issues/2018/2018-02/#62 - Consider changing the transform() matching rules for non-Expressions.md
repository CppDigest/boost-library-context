# #62 - Consider changing the transform() matching rules for non-Expressions [Closed]

> Username: tzlaine  
> Created at: 2018-02-21 17:09:27 UTC  
> Updated at: 2018-06-11 17:16:20 UTC  
> Closed at: 2018-02-23 06:01:47 UTC  
> Url: https://github.com/boostorg/yap/issues/62  

>>   Actually...  I have a much better idea.  Why  
>> don't you allow transform for a non-expr to match  
>> a terminal tag transform?  
>  
>  
> I've read this a few times now and cannot parse.  Could you rephrase?  
>  
  
struct F {  
  auto operator()(terminal_tag, int x) { return x * 2; }  
};  
  
transform(3, F{}); // returns 6  
  
This allows you to avoid the need for as_expr.  
I think this behavior is consistent, when you  
also unwrap terminals, as you would essentially  
be treating terminals and the raw values as  
being interchangeable.  
  
I like this idea at first glance, but I'll need to look at the places where transform() applied to a non-Expression is acting as a no-op for a reason.
