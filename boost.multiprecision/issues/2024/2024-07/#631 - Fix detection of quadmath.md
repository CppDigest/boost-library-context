# #631 - Fix detection of quadmath [Closed]

> Username: mborland  
> Created at: 2024-07-19 19:55:33 UTC  
> Updated at: 2024-07-24 15:19:06 UTC  
> Closed at: 2024-07-24 15:19:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/631  

From the mailing list:  
  
```  
Hello,  
  
because of a bug[1] in GCC 13 and earlier versions, the #include of  
quadmath.h in multiprecision/cpp_bin_float.hpp did not fail even when  
the header file was not present on the system and compilation succeeded  
as long as nothing from the header file was actually needed.  
  
The bug has been fixed in GCC 14 which all of a sudden leads to new  
build requirements and failures such as [2].  Examining this build  
failure, I noticed that in math/special_functions/fpclassify.hpp (which  
is indirectly included from multiprecision/cpp_bin_float.hpp) the  
existence of the header file is specifically tested and macro  
BOOST_MATH_HAS_QUADMATH_H is defined or not accordingly.  
  
So I wanted to ask the question in the $subject.  Could, or perhaps even  
should the include (together with its uses introduced in commit  
4911125248bd) be guarded also by BOOST_MATH_HAS_QUADMATH_H and not just  
by BOOST_HAS_FLOAT128?  
  
Thank you,  
  
Martin  
  
[1] https://gcc.gnu.org/bugzilla/show_bug.cgi?id=80753  
[2] https://bugzilla.opensuse.org/show_bug.cgi?id=1225861#c7  
```  
  
@jzmaddock I'm not sure if it's better to just copy paste the logic out of math, or upstream the solution to gcc.hpp in config. Math doesn't use config in standalone mode, but multiprecision provides it in the package.
