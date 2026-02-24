# #10 CMake improvements [Open]

> Username: Flamefire  
> Created at: 2019-06-12 06:52:48 UTC  
> Updated at: 2019-06-12 08:27:22 UTC  
> Url: https://github.com/boostorg/boost_install/pull/10  

@pdimov I created a PR for discussing proposed changes which is easier than the mailing list.  
  
Basicall what I found:  
- BUG: `_Boost_INCLUDEDIR/LIBRARYDIR` are not unset for interface targets  
  - Moved down to where it was used and removed right after  
- lot's of manual `unset` for "temporary" variables.  
  - Used `function` which adds a scope. To set "persistent" variables one needs to explicitely use `set(... PARENT_SCOPE)`. I believe this makes it much more clear what is used where and passed back  
- Code duplication in generated files and lot's of (mostly) static CMake code embedded in the bjam files making maintenance harder  
  - Factor out the search for variants into own function in own file.  
  - Interface could probably be even further reduced to 1 parameter if the 2nd is always `boost_<name>`, but I wasn't entirely sure. Could you try to validate the behaviour especially for the (versioned) python stuff? I think there is just `boost_python` and a `Boost_PYTHON_VERSION` variable, isn't it?

---
