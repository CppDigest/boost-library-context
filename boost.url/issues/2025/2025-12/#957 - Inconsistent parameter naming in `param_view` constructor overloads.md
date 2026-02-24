# #957 - Inconsistent parameter naming in `param_view` constructor overloads [Closed]

> Username: alandefreitas  
> Created at: 2025-12-19 21:19:14 UTC  
> Updated at: 2026-01-20 17:00:49 UTC  
> Closed at: 2026-01-20 17:00:49 UTC  
> Url: https://github.com/boostorg/url/issues/957  

In the aggregated reference page for `boost::urls::param_view` constructors, equivalent parameters are named differently across overloads. When all overloads are shown together, the inconsistent naming makes the API harder to read and suggests semantic differences that don’t actually exist.  
  
Reference:  
https://www.boost.org/doc/libs/master/doc/antora/url/reference/boost/urls/param_view/2constructor-0e.html  
  
Examples include:  
- `key` vs `key_`  
- `value` vs `value_`  
- `has_value` vs `has_value_`  
  
Each overload makes sense on its own, but the differences become distracting when aggregated. We should normalize parameter names across overloads.
