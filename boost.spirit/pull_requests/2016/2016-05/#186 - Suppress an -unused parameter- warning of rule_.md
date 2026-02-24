# #186 Suppress an "unused parameter" warning of rule_ [Merged]

> Username: dkim  
> Created at: 2016-05-04 20:28:15 UTC  
> Updated at: 2016-05-04 20:34:44 UTC  
> Merged at: 2016-05-04 20:30:23 UTC  
> Closed at: 2016-05-04 20:30:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/186  

The `rule_` parameter is not used in `parse_rule`, which `BOOST_SPIRIT_DEFINE` expands to. GCC with `-Wunused-parameter` (often indirectly activated by `-Wall -Wextra`) issues a warning when a function parameter is not used in its function body.

---
