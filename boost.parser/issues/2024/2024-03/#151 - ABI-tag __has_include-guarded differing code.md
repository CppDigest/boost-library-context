# #151 - ABI-tag __has_include-guarded differing code [Closed]

> Username: tzlaine  
> Created at: 2024-03-01 01:10:26 UTC  
> Updated at: 2024-03-15 21:29:34 UTC  
> Closed at: 2024-03-15 21:29:34 UTC  
> Url: https://github.com/boostorg/parser/issues/151  

From David Sankel and me, from the Boost review:  
  
> The usage of has_include is a ODR nightmare for large codebases. It would  
> be better to generate some kind of config.hpp that sets these definitions  
> at compile time.  
  
This is a great point.  There are three places where this currently happens:  
  
1) Boost.TypeIndex vs typeinfo (for printing type names in the trace)  
2) BOOST_ASSERT vs. assert  
3) Spirit X3 vs. charconv vs. Boost.Charconv  
  
1 and 3 are easily addressed by adding an extra template parameter to the few function templates that use those APIs, so a mismatch will be a build break, not ODR.  
2 does not apply to production code, and any use of BOOST_ASSERT already has the same problem anyway.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-03-04 03:54:43 UTC  
> Url: https://github.com/boostorg/parser/issues/151#issuecomment-1975652007  

The technique I used for this was not explicit ABI-tagging (using the GCC+Clang abi_tag attribute), because there does not appear to be an MSVC equivalent.  Instead, I put each alternate implementation in its own inline namespace.
