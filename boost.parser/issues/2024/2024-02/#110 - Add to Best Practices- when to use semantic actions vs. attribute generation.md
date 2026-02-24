# #110 - Add to Best Practices: when to use semantic actions vs. attribute generation [Closed]

> Username: tzlaine  
> Created at: 2024-02-23 01:22:07 UTC  
> Updated at: 2024-03-15 21:29:33 UTC  
> Closed at: 2024-03-15 21:29:33 UTC  
> Url: https://github.com/boostorg/parser/issues/110  

From Christian Mazakas in the Boost review:  
  
> The documentation doesn't exactly give users any guidelines about when they  
> should formally  
> define rules with attributes vs semantic actions.  
>  
> This is one of those things where if users don't already have the intuition  
> built up, using the  
> library is an exercise in frustration.  
>  
> Namely, attributes don't compose well when you start nesting rules and want  
> to have different out  
> params at different levels of the parsers. I suppose this should be obvious  
> because the rules themselves  
> form a hierarchy so the attributes _must_ mirror that.  
>  
> I found that the best thing to do for my use-case was to abuse  
> `_globals(ctx)` and semantic actions.  
>  
> I think a good rule of thumb for users can be expressed as something like:  
> use rule attributes for simple  
> grammars where all you really want is just the `std::vector<double>`. For  
> something complex with domain-defined  
> validation rules, prefer `_globals(ctx)` and semantic actions for  
> validating everything.
