# #71 - Multiple definitions linker issue [Closed]

> Username: voivoid  
> Created at: 2019-04-23 06:29:19 UTC  
> Updated at: 2019-08-25 08:32:06 UTC  
> Closed at: 2019-08-25 08:32:06 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/71  

Hi!  
  
I see a linker error while I try to use safe_numerics in at least two translation units:  
```  
/usr/bin/ld: ../problems/CMakeFiles/AocProblems.dir/src/2017/problem_18.cpp.o: in function `boost::safe_numerics::make_error_code(boost::safe_numerics::safe_numerics_error const&)':  
problem_18.cpp:(.text+0x0): multiple definition of `boost::safe_numerics::make_error_code(boost::safe_numerics::safe_numerics_error const&)'; ../problems/CMakeFiles/AocProblems.dir/src/2017/problem_07.cpp.o:problem_07.cpp:(.text+0x0): first defined here  
```  
  
Declaring make_error_code function as an inline fixes the problem  
  
Tested with clang 8.0 / gcc 8.3.0 and boost 1.70

---

## Comment 1

> Username: robertramey  
> Created at: 2019-04-23 06:44:00 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/71#issuecomment-485664349  

`  
// constexpr - damn, can't use constexpr due to std::error_code  
std::error_code make_error_code(const safe_numerics_error & e){  
    return std::error_code(static_cast<int>(e), safe_numerics_error_category);  
}  
`  
Looks like a good call.  Note the comment I placed here.  I'll make the change on my development system and let it migrate to the next boost version.  
  
Thanks for figuring this out and letting me know.  
  
Robert Ramey

---

## Comment 2

> Username: voivoid  
> Created at: 2019-08-25 08:32:06 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/71#issuecomment-524611685  

Fixed in boost 1.71  
  
Thanks!
