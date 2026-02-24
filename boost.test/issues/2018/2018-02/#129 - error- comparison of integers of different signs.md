# #129 - error: comparison of integers of different signs [Closed]

> Username: hrubymar10  
> Created at: 2018-02-05 13:44:30 UTC  
> Updated at: 2018-02-06 08:52:39 UTC  
> Closed at: 2018-02-06 08:52:39 UTC  
> Url: https://github.com/boostorg/test/issues/129  

I got 3 errors while compiling https://github.com/wesnoth/wesnoth unit_tests  
Log of errors:  
http://ix.io/FhW  
  
I got them when I doesn't have `isystem ./Headers` flag set in C compiler flags.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-02-05 21:52:58 UTC  
> Url: https://github.com/boostorg/test/issues/129#issuecomment-363233921  

As the errors say:  
```  
error: comparison of integers of different signs: 'const unsigned long' and 'const int' [-Werror,-Wsign-compare]  
```  
the compilation line is promoting any warning to an error (``-Werror``) and the comparison of integers of different sign is raising a warning (``-Wsign-compare``). This is not exactly a boost.test issue.  
Why not trying without those 2 options?

---

## Comment 2

> Username: jyrkive  
> Created at: 2018-02-06 05:27:28 UTC  
> Url: https://github.com/boostorg/test/issues/129#issuecomment-363315030  

So Boost.Test isn't supposed to be clean of compiler warnings?

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2018-02-06 07:53:21 UTC  
> Url: https://github.com/boostorg/test/issues/129#issuecomment-363339451  

It is, but what is triggering the warning is the user code here. This on the user side will generate an error with the current options  
  
```  
int var1 = 0;  
long int var2 = 1;  
if(var1 != var2) {  
    std::cout << "variables are different" << std::endl;  
}  
```

---

## Comment 4

> Username: jyrkive  
> Created at: 2018-02-06 08:14:07 UTC  
> Updated at: 2018-02-06 08:15:33 UTC  
> Url: https://github.com/boostorg/test/issues/129#issuecomment-363343802  

In @hrubymar10 's log there isn't any user code involved.  
  
(File names and line numbers emphasized.)  
  
> In file included from /Users/user/xcode/wesnoth/src/tests/test_serialization.cpp:21:  
> In file included from Headers/boost/test/auto_unit_test.hpp:16:  
> In file included from Headers/boost/test/unit_test.hpp:18:  
> In file included from Headers/boost/test/test_tools.hpp:46:  
> **Headers/boost/test/tools/old/impl.hpp:107:17**: error: comparison of integers of different signs: 'const unsigned int' and 'const wchar_t' [-Werror,-Wsign-compare]  
>     return left == right;  
>            ~~~~ ^  ~~~~~  
> **Headers/boost/test/tools/old/impl.hpp:130:16**: note: in instantiation of function template specialization 'boost::test_tools::tt_detail::equal_impl<unsigned int, wchar_t>' requested here  
>         return equal_impl( left, right );  
>                ^  
> **Headers/boost/test/tools/old/impl.hpp:145:16**: note: in instantiation of function template specialization 'boost::test_tools::tt_detail::equal_impl_frwd::call_impl<unsigned int, wchar_t>' requested here  
>         return call_impl( left, right, left_is_array() );  
>                ^  
> **Headers/boost/test/tools/old/impl.hpp:92:50**: note: in instantiation of function template specialization 'boost::test_tools::tt_detail::equal_impl_frwd::operator()<unsigned int, wchar_t>' requested here  
> BOOST_PP_REPEAT( BOOST_TEST_MAX_PREDICATE_ARITY, IMPL_FRWD, _ )

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2018-02-06 08:20:14 UTC  
> Url: https://github.com/boostorg/test/issues/129#issuecomment-363345117  

Those ``left`` and ``right`` are the types of the variables that are being compared in a ``BOOST_TEST`` statement.

---

## Comment 6

> Username: jyrkive  
> Created at: 2018-02-06 08:51:59 UTC  
> Url: https://github.com/boostorg/test/issues/129#issuecomment-363352782  

Ah, you're right. The error messages are so long that I missed that they still continued (and pointed to our code). Sorry. :(  
  
I have fixed this in our end. This bug report can be closed.
