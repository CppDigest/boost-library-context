# #43 - Verify that this form of the get_arity example works [Closed]

> Username: tzlaine  
> Created at: 2018-02-16 20:09:49 UTC  
> Updated at: 2018-06-11 17:16:19 UTC  
> Closed at: 2018-02-20 07:04:23 UTC  
> Url: https://github.com/boostorg/yap/issues/43  

I'd guess that tag matching is a later addition, because it only works for simple cases. You can't do  
  
   auto operator()(any_expression_tag, T&&... t);  
  
I'm don't understand what that would mean, exactly.  What you can write though is:  
  
template <typename Tag, typename... T>  
auto operator()(Tag, T &&... t)  
  
Is that different from the intended use case above?  
   
[snip]  
  
template<expr_kind kind, class... T>  
auto get_arity::operator()(expression_tag<kind>, T const&... t) const  
{  
   return std::max( { yap::transform(t, get_arity{})... } );  
  
}  
  
I'm pretty sure you actually can write almost exactly this using the template I showed above.  I'll verify that this is the case.  That might make the example a lot easier to understand.
