# #30 - Undefined behavior in negation of load_mode::type [Closed]

> Username: Flamefire  
> Created at: 2019-08-05 15:54:15 UTC  
> Updated at: 2020-07-12 19:42:46 UTC  
> Closed at: 2019-11-23 17:59:09 UTC  
> Url: https://github.com/boostorg/dll/issues/30  

The enum `type` has no underlying type specified but the bitwise negation operator converts a negated `unsigned int` to `type` which almost certainly results in a type outside the range of the enum. UBSAN (clang++-8) catches this: `/opt/boost_1_67_0/include/boost/dll/shared_library_load_mode.hpp:226:19: runtime error: load of value 4278190079, which is not a valid value for type 'boost::dll::load_mode::type'`  
  
Solution: Specify the type (unsigned) explicitly.

---

## Comment 1

> Username: alextibbles  
> Created at: 2020-07-12 19:42:46 UTC  
> Url: https://github.com/boostorg/dll/issues/30#issuecomment-657266513  

The following patch against boost 1_71 prevents this being triggered in my use-case (and I hope just as clearly express the intent to clear the bit):  
```  
110c110  
<         mode &= ~load_mode::search_system_folders;  
---  
>         if (mode & load_mode::search_system_folders) mode ^= load_mode::search_system_folders;  
114c114  
<             mode &= ~load_mode::append_decorations;  
---  
>             mode ^= load_mode::append_decorations;  
```  
I notice the following comment in the TravisCI setup  
` # `-fsanitize=enum` causes "boost/dll/shared_library_load_mode.hpp:226:19: runtime error: load of value 4278190079, which is not a valid value for type 'boost::dll::load_mode::type'"`  
  
These look to be the only 2 uses of negation in the library itself, so could be useful to boost.dll users running the default set of UBSAN checks. It doesn't seem to be sensible to conditionalize on C++17. I could try to submit this change
