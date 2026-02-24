# #48 Ticket 12754 - sfinea friendly boost::range_details::replace_holder overloads  [Merged]

> Username: t-wynants  
> Created at: 2017-01-12 16:56:30 UTC  
> Updated at: 2017-07-12 13:19:44 UTC  
> Merged at: 2017-06-30 11:45:34 UTC  
> Closed at: 2017-06-30 11:45:34 UTC  
> Url: https://github.com/boostorg/range/pull/48  

include\boost\range\adaptor\replaced.hpp  
  
Make sure that the overload resolution doesn't choke on the value type factory when its considers the overload

---

## Comment 1

> Username: morinmorin  
> Created_at: 2017-07-06 15:22:57 UTC  
> Url: https://github.com/boostorg/range/pull/48#issuecomment-313429189  

Indeed, the original code is problematic. Good catch!  
  
I have a few comments on this PR:   
  
1.   
   Default template arguments in function templates are a C++11 feature. Unconditional use of such a feature **breaks C++03 build**. Anyway, since `RangeValueType` is deduced, the default template argument (i.e. `= ... range_value<...>::type`) is not used at all and thus unnecessary.   
  
2.   
   The patched code behaves in a slightly different manner than the original code. With the original code, this code fails to compile.  
   ```  
   int array[3] = {1, 2, 3};  
   array | replaced(2.0, 3.5);  
   ```  
   But, with the patched code, this compiles without errors. The implicit conversion is sometimes the cause of bugs, but I think the new code is acceptable as this is the specified behavior in the documentation (see "Precondition").   
  
   For the consistent behavior between `replaced` and `replaced_if`, I think it would be better to add `Value` template parameter to `replaced_if` too.   
  
3.   
   What is SFINAE-unfriendly is `range_value` trait (and other traits used by `range_value`). If they are modified to be SFINAE-friendly, then the original code (in `replaced.hpp`) works fine. But I agree that adding `Value` template parameter to `replaced` would be much easier than modifying the traits classes.   
  
  
In short, please remove the default template argument (`= BOOST_DEDUCED_TYPENAME range_value<SinglePassRange>::type`).

---

## Comment 2

> Username: morinmorin  
> Created_at: 2017-07-12 13:19:44 UTC  
> Url: https://github.com/boostorg/range/pull/48#issuecomment-314766555  

I've just submitted PR #53 to fix breakage in C++03 build.

---
