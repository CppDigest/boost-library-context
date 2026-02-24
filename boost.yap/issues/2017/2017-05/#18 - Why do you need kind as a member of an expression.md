# #18 - Why do you need kind as a member of an expression? [Closed]

> Username: daixtrose  
> Created at: 2017-05-10 20:51:34 UTC  
> Updated at: 2018-06-11 17:16:18 UTC  
> Closed at: 2017-05-11 00:04:09 UTC  
> Url: https://github.com/boostorg/yap/issues/18  

Is there a technical reason for storing the value template argument in the field ```static const boost::yap::expr_kind``` of an expression?   
  
IMHO this is redundant and could be replaced by a utility method that extracts the value from the template argument.

---

## Comment 1

> Username: tzlaine  
> Created at: 2017-05-11 00:04:07 UTC  
> Url: https://github.com/boostorg/yap/issues/18#issuecomment-300645723  

Yes, there is a technical reason for it.  my_expr<kind, tuple> is not a template, it is a type instantiated from a template.  As an outside observer (say, a yap function template that acts on expressions), I can't get at the Kind template parameter of a my_expr<kind, tuple>, because it no longer exists.  
  
Just as importantly, if you want me to make a massive change to the library, you need to provide a compelling case as to why.  Just pointing out it could be done another way is not reason enough.
