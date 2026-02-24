# #1137 - Problem setting BOOST_MATH_RATIONAL_METHOD in user.hpp [Closed]

> Username: WarrenWeckesser  
> Created at: 2024-05-22 03:38:23 UTC  
> Updated at: 2024-05-22 14:03:10 UTC  
> Closed at: 2024-05-22 10:43:39 UTC  
> Url: https://github.com/boostorg/math/issues/1137  

According to the documentation at  
  
https://www.boost.org/doc/libs/1_85_0/libs/math/doc/html/math_toolkit/tuning.html  
  
I can set the method for evaluating a rational function by assigning the appropriate value to BOOST_MATH_RATIONAL_METHOD in boost/math/tools/user.hpp. However, if I do this, I get a warning, because there is code in boost/math/tools/config.hpp that defines BOOST_MATH_RATIONAL_METHOD without checking if it has already been defined in user.hpp.  
  
In my case, I tried setting BOOST_MATH_RATIONAL_METHOD to 1 in user.hpp, and I got the warning  
  
```  
In file included from /Users/warren/repos/git/forks/boostorg/math/include/boost/math/tools/rational.hpp:14:  
/Users/warren/repos/git/forks/boostorg/math/include/boost/math/tools/config.hpp:394:11: warning: 'BOOST_MATH_RATIONAL_METHOD' macro redefined [-Wmacro-redefined]  
#  define BOOST_MATH_RATIONAL_METHOD 3  
          ^  
/Users/warren/repos/git/forks/boostorg/math/include/boost/math/tools/user.hpp:27:9: note: previous definition is here  
#define BOOST_MATH_RATIONAL_METHOD 1  
        ^  
1 warning generated.  
```  
  
  
In config.hpp, user.hpp is included at [line 219](https://github.com/boostorg/math/blob/52be1ad7f203945b874078e969703b105dceeba5/include/boost/math/tools/config.hpp#L219).  There is a lot code after this that defines macros based on the compiler and platform.  The problem seems to be that this code does not check if the macros are already defined. In my case, I get the redefinition of BOOST_MATH_RATIONAL_METHOD at [line 394](https://github.com/boostorg/math/blob/52be1ad7f203945b874078e969703b105dceeba5/include/boost/math/tools/config.hpp#L394) of config.hpp.  
  
Later in config.hpp is another line ([line 429](https://github.com/boostorg/math/blob/52be1ad7f203945b874078e969703b105dceeba5/include/boost/math/tools/config.hpp#L429)) that defines BOOST_MATH_RATIONAL_METHOD, but this one is only activated if BOOST_MATH_RATIONAL_METHOD is not already defined.

---

## Comment 1

> Username: mborland  
> Created at: 2024-05-22 06:43:53 UTC  
> Url: https://github.com/boostorg/math/issues/1137#issuecomment-2123991253  

Looks like we have the same issue in our own test sets: https://github.com/boostorg/math/actions/runs/9171166088/job/25215137805?pr=1136#step:11:1480. Will investigate.

---

## Comment 2

> Username: WarrenWeckesser  
> Created at: 2024-05-22 14:03:08 UTC  
> Url: https://github.com/boostorg/math/issues/1137#issuecomment-2124882547  

Thanks for the quick fix @mborland!
