# #936 - stateless token_rule_t specialization [Closed]

> Username: vinniefalco  
> Created at: 2025-10-22 19:39:46 UTC  
> Updated at: 2026-01-23 03:54:32 UTC  
> Closed at: 2026-01-23 03:54:32 UTC  
> Url: https://github.com/boostorg/url/issues/936  

`token_rule_t` should be specialized so when the `CharSet` is empty the rule is empty and the constructor is a default constructor. However it is in the implementation_defined namespace which may require some API redesign.
