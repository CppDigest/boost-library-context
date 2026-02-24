# #232 Fix dangling refs in debug of call_rule_defintion` [Merged]

> Username: sehe  
> Created at: 2017-04-28 23:11:08 UTC  
> Updated at: 2017-04-29 01:05:46 UTC  
> Merged at: 2017-04-29 01:05:46 UTC  
> Closed at: 2017-04-29 01:05:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/232  

There is an issue in `call_rule_definition` where it tries to print the  
attribute before transformation (via `context_debug` -> `simple_trace`).  
  
However, it breaks if the made attribute type doesn't match the actual  
attribute type. In this case, it makes `context_debug` store a dangling  
reference to a temporary converted attribute, leading to Undefined  
Behaviour.  
  
See also https://stackoverflow.com/a/43688320/85371

---
