# #58 - clang: importing operator++ from CRTP interface via `using` only compiles in trunk [Closed]

> Username: hgkjshegfskef  
> Created at: 2022-11-09 23:36:17 UTC  
> Updated at: 2022-11-10 10:46:41 UTC  
> Closed at: 2022-11-10 10:46:41 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/58  

https://godbolt.org/z/7qEnvME4n  
  
When importing the postfix `operator++` (following the tutorials/examples), it seems like clang barely accepts it, only trunk version compiles. Perhaps something can be relaxed so that older versions of clang can build the code too? Or is it due to concepts? The error message is not very descriptive.

---

## Comment 1

> Username: hgkjshegfskef  
> Created at: 2022-11-10 10:46:41 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/58#issuecomment-1310095000  

This is probably due to concepts still being wacky, as `BOOST_STL_INTERFACES_DISABLE_CONCEPTS` makes it work with older versions.
