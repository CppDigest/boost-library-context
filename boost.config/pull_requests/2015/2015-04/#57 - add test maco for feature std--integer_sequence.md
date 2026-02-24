# #57 add test maco for feature std::integer_sequence [Closed]

> Username: olk  
> Created at: 2015-04-22 11:46:10 UTC  
> Updated at: 2015-04-22 18:19:34 UTC  
> Closed at: 2015-04-22 18:19:34 UTC  
> Url: https://github.com/boostorg/config/pull/57  

- std::integer_sequence is a C++14 feature  
- SD-6 macro __cpp_lib_integer_sequence is used to test this feature  
- __cpp_lib_integer_sequence is defined in header <utility>  
- if std::integer_sequence is not available, BOOST_NO_CXX14_INTEGER_SEQUENCE is defined

---

## Comment 1

> Username: olk  
> Created_at: 2015-04-22 11:46:37 UTC  
> Updated_at: 2015-04-22 12:12:48 UTC  
> Url: https://github.com/boostorg/config/pull/57#issuecomment-95146985  

sorry - this patch modifies only GCC and Clang - I'm not familiar with the other compilers

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2015-04-22 18:19:34 UTC  
> Url: https://github.com/boostorg/config/pull/57#issuecomment-95290185  

Oliver, I'm sorry but I'm going to reject this as is, because:  
- This is a std lib feature not a compiler one, macros should be moved appropriately.  
- The macro should be defined in all config headers under /stdlib/ where this is unsupported (or unknown whether it is).  Each should have a section at the end already for "things that are always defined".  
- We need a test case and docs.  
  
Please see also http://www.boost.org/doc/libs/1_57_0/libs/config/doc/html/boost_config/guidelines_for_boost_authors.html#boost_config.guidelines_for_boost_authors.adding_new_defect_macros  
  
If you don't submit a revised PR by the weekend, I might take care of this anyway, especially if you remind me!

---
