# #815 Modernize `x3::action` [Merged]

> Username: saki7  
> Created at: 2025-09-07 17:50:10 UTC  
> Updated at: 2025-09-09 22:55:19 UTC  
> Merged at: 2025-09-09 22:55:15 UTC  
> Closed at: 2025-09-09 22:55:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/815  

Part of boostorg/spirit_x4#4   
  
`x3::action` now uses concepts to dispatch `f(ctx)` or `f()`, depending on   
whether `f` accepts such signature.  
  
The dispatching implementation is moved from `call.hpp` to `action.hpp`,  
 as the function is only used from the `x3::action` class.  
  
`operator/`: Deprecated. The symbol `/` normally means "ordered choice"  
in PEG, and is irrelevant to semantic actions. Furthermore, using C++'s   
`operator/` for this purpose may introduce surprising behavior when it's   
mixed with ordinary PEG operators, for instance, the unary `operator+`,   
due to precedence.

---
