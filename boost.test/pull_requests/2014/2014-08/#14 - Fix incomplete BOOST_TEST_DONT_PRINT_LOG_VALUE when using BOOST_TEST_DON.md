# #14 Fix incomplete BOOST_TEST_DONT_PRINT_LOG_VALUE when using BOOST_TEST_DON... [Closed]

> Username: mkurdej  
> Created at: 2014-08-23 17:25:36 UTC  
> Updated at: 2016-02-12 13:02:20 UTC  
> Closed at: 2014-12-11 09:01:24 UTC  
> Url: https://github.com/boostorg/test/pull/14  

When we use a user-defined error without overloaded operator<<(std::ostream&, T const&), we have to use BOOST_TEST_DONT_PRINT_LOG_VALUE in order to prevent UTF to print output to log.  
BOOST_TEST_DONT_PRINT_LOG_VALUE works correctly only if you use macros like BOOST_CHECK_EQUAL.  
However, BOOST_CHECK_EQUAL_COLLECTIONS provokes compile-time error.  
  
Fixes https://svn.boost.org/trac/boost/ticket/9390.  
  
Error messages:  
// MSVC:  
//  ./boost/test/utils/wrap_stringstream.hpp(66) : error C2679: binary '<<' : no operator found which takes a right-hand operand of type 'const MyClass' (or there is no acceptable conversion)  
// GCC:  
// ./boost/test/utils/wrap_stringstream.hpp:66:19: error: no match for 'operator<<' (operand types are 'boost::basic_wrap_stringstream<char>::wrapped_stream {aka std::basic_ostringstream<char>}' and 'const MyClass')  
//     targ.stream() << t;

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2014-12-10 17:18:54 UTC  
> Url: https://github.com/boostorg/test/pull/14#issuecomment-66487733  

I think this is now addressed by the commit  
https://github.com/boostorg/test/commit/8d06b9e3dee7d7a9597ca1e7ceb62e92fbef0422  
  
Would you please test the develop branch on your side?  
Thanks for the patch.

---

## Comment 2

> Username: mkurdej  
> Created_at: 2014-12-11 08:59:54 UTC  
> Url: https://github.com/boostorg/test/pull/14#issuecomment-66589961  

Tested on Win8.1, MSVC 2013, 32/64-bit. Works fine.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2014-12-11 09:01:21 UTC  
> Url: https://github.com/boostorg/test/pull/14#issuecomment-66590100  

Thanks!

---
