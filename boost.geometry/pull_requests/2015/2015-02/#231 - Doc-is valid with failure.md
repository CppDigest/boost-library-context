# #231 Doc/is valid with failure [Merged]

> Username: mkaravel  
> Created at: 2015-02-25 13:27:56 UTC  
> Updated at: 2015-02-25 17:12:01 UTC  
> Merged at: 2015-02-25 17:06:12 UTC  
> Closed at: 2015-02-25 17:06:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/231  

This PR adds documentation for:  
- The `validity_failure_type` enumeration type.  
- The overload of `is_valid()` that takes a reference to a `validity_failure_type` enumeration value as a 2nd argument.  
- Various other minor fixes related to the documentation of `is_valid()`.  
  
Among other things, this PR contains a change in the doxygen-to-quickbook output for enumeration values (see commit 5bc4de3). The change refers to how enumeration types with many values are displayed in the BG documentation. More precisely, enumeration types with more than 4 values are not displayed in a single line, but rather each enum value is displayed in a separate line. See the documentation of `validity_failure_type` where this feature is used (this enum type was the motivation for this change).

---
