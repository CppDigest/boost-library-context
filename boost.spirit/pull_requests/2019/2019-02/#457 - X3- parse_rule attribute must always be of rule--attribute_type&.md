# #457 X3: parse_rule attribute must always be of rule::attribute_type& [Merged]

> Username: Kojoley  
> Created at: 2019-02-08 19:45:23 UTC  
> Updated at: 2019-02-09 11:57:57 UTC  
> Merged at: 2019-02-09 11:57:54 UTC  
> Closed at: 2019-02-09 11:57:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/457  

After #456 `parse_rule` is always called with value of `rule::attribute_type&`  
type. By removing attribute type deduction we also will turn any possible bug  
in `transform_attribute` from a linkage to compile error.  
  
Fixes #454

---
