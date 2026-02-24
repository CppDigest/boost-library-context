# #217 Fix/templatize relare turns by rescale policy [Merged]

> Username: mkaravel  
> Created at: 2015-02-11 11:39:32 UTC  
> Updated at: 2015-02-11 13:07:06 UTC  
> Merged at: 2015-02-11 13:00:54 UTC  
> Closed at: 2015-02-11 13:00:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/217  

The purpose of this PR is to extend the interface for `bg::relate::get_turns` so that it can accept the robustness policy type as a template parameter (and then pass on an appropriate object to `dispatch::get_turns`).  
  
By default the no-rescale-policy is used for backward compatibility with the older version of the code.

---
