# #161 Disable msvc warning that can occur under some versions+settings [Merged]

> Username: balagansky-work  
> Created at: 2023-07-26 23:05:29 UTC  
> Updated at: 2023-08-03 03:13:57 UTC  
> Merged at: 2023-08-03 03:13:57 UTC  
> Closed at: 2023-08-03 03:13:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/161  

This warning has been observed under more than one version. One specific example, as described in issue #138, is on indirect_streambuf.hpp(440) when Whole Program Optimization is off. In this case, the line before is seen by the optimizer as always throwing for particular instantiations of the template, and therefore the line in question is flagged as not reachable.  
  
MSVC team has stated this is is expected behavior. (https://developercommunity.visualstudio.com/t/Unexpected-C4702-Unreachable-code-in-boo/10410775)  
  
This is a simple and hopefully good-enough fix.

---
