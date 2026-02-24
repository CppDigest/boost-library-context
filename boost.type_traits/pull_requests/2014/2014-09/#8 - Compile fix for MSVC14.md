# #8 Compile fix for MSVC14 [Merged]

> Username: MarcelRaad  
> Created at: 2014-09-24 12:32:57 UTC  
> Updated at: 2014-09-25 09:42:55 UTC  
> Merged at: 2014-09-25 09:34:44 UTC  
> Closed at: 2014-09-25 09:34:44 UTC  
> Url: https://github.com/boostorg/type_traits/pull/8  

MSVC14 supports noexcept, but doesn't support expression SFINAE, so false_or_cpp11_noexcept_move_assignable<const T> fails to compile with  
error C3892: 'boost::declval': you cannot assign to a variable that is const  
  
Disabling the overload for const types so that they are never reported as noexcept move assignable fixes the compile error. Of course this doesn't work for imaginary UDTs with an assignment operator that is marked both noexcept and const - the alternative would be to guard the block with BOOST_NO_SFINAE_EXPR in addition to BOOST_NO_CXX11_NOEXCEPT.

---
