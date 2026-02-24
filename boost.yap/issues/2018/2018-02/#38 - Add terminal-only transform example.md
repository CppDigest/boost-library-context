# #38 - Add terminal-only transform example [Closed]

> Username: tzlaine  
> Created at: 2018-02-16 00:15:53 UTC  
> Updated at: 2018-06-11 17:16:19 UTC  
> Closed at: 2018-02-20 07:04:23 UTC  
> Url: https://github.com/boostorg/yap/issues/38  

Based on this:  
  
 I think something like this should be usable in most cases:  
  
// Let's assume the existence of a my_make_term() function that takes a Yap terminal  
// and a Context &, and returns a new Yap terminal.  
template <typename Context>  
struct transform_terminals_with_context  
{  
    // Base case. Note that I'm ignoring placeholders entirely for this  
    // example (they're easy to special-case if necessary).  
    template <typename T>  
    auto operator() (yap::terminal_tag, T && t)  
    { return my_make_term(std::forward<T>(t), context_); }  
  
    // Recursive case: Match any binary expression.  
    template <typename Tag, typename LExpr, typename RExpr>  
    auto operator() (Tag, LExpr const & left, RExpr const & right)  
    {  
        return yap::make_expression<yap::detail::kind_for<Tag>>(  
            boost::yap::transform(left, *this),  
            boost::yap::transform(right, *this));  
    }  
  
    // Recursive case: Match any unary expression.  
    template <typename Tag, typename Expr>  
    auto operator() (Tag, Expr const & expr)  
    {  
        return yap::make_expression<yap::detail::kind_for<Tag>>(  
            boost::yap::transform(expr, *this));  
    }  
  
    // Ternary and call are added as necessary.  
  
    Context & context_;  
};  
  
This transforms all your terminals to new terminals, using your custom code that applies your terminal + context operation.  You can then just eval() the transformed expression using the default evaluate(), transform() it again with a different transform, etc.  I had to make up the constexpr function kind_for<>() that maps expression-kind tag types to yap::expr_kind values (which I'll probably add now that I see it is needed *TODO*), but the reset is just typical Yapery.
