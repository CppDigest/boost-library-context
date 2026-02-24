# #280 Declare non-ODR-used copy constructor [Merged]

> Username: ecatmur  
> Created at: 2023-02-22 20:01:18 UTC  
> Updated at: 2023-08-29 01:29:14 UTC  
> Merged at: 2023-08-23 18:51:24 UTC  
> Closed at: 2023-08-23 18:51:24 UTC  
> Url: https://github.com/boostorg/serialization/pull/280  

The compiler-generated copy constructor is deprecated since C++11 on classes with user-declared copy assignment operator.  
  
This change allows clean compilation with the -Wdeprecated-copy/-Wdeprecated-copy-with-user-provided-ctor flag.

---

## Comment 1

> Username: robertramey  
> Created_at: 2023-08-23 21:46:05 UTC  
> Url: https://github.com/boostorg/serialization/pull/280#issuecomment-1690687947  

Did you actually test this?  Library fails to compile after marging this change:   
  
`boost::archive::xml::assign_impl<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>>> const&) in xml_grammar.o  
ld: symbol(s) not found for architecture x86_64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)`

---

## Comment 2

> Username: ecatmur  
> Created_at: 2023-08-29 01:29:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/280#issuecomment-1696638116  

> Did you actually test this? Library fails to compile after marging this change:  
>   
> `boost::archive::xml::assign_impl<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>>> const&) in xml_grammar.o ld: symbol(s) not found for architecture x86_64 clang: error: linker command failed with exit code 1 (use -v to see invocation)`  
  
Apparently not, sorry. Not sure how that got through. #297 for the correct fix.

---
