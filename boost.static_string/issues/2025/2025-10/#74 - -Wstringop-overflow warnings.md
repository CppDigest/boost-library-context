# #74 - -Wstringop-overflow warnings [Closed]

> Username: ashtum  
> Created at: 2025-10-22 20:07:48 UTC  
> Updated at: 2025-10-29 17:53:43 UTC  
> Closed at: 2025-10-29 17:53:43 UTC  
> Url: https://github.com/boostorg/static_string/issues/74  

static_string related warnings appear while building the Beast tests.    
This is the test file (Beast used to have its own implementation of static_string, which has since been replaced with Boost.StaticString, but the tests are still there):  
https://github.com/boostorg/beast/blob/develop/test/beast/core/static_string.cpp  
```  
In function ‘assign’,  
    inlined from ‘copy_with_traits’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:306:19,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:6540:37,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2316:11,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2281:18,  
    inlined from ‘testOperations’ at /workspace/boost/libs/beast/test/beast/core/static_string.cpp:616:26:  
/usr/include/c++/13/bits/char_traits.h:358:14: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
  358 |         __c1 = __c2;  
      |              ^  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp: In member function ‘testOperations’:  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp:615:34: note: at offset 8 into destination object ‘s2’ of size 8  
  615 |                 static_string<6> s2("12345");  
      |                                  ^  
In function ‘assign’,  
    inlined from ‘copy_with_traits’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:306:19,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:6540:37,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2316:11,  
    inlined from ‘testOperations’ at /workspace/boost/libs/beast/test/beast/core/static_string.cpp:642:26:  
/usr/include/c++/13/bits/char_traits.h:358:14: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
  358 |         __c1 = __c2;  
      |              ^  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp: In member function ‘testOperations’:  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp:641:34: note: at offset 8 into destination object ‘s2’ of size 8  
  641 |                 static_string<6> s2("12345");  
      |                                  ^  
In function ‘move’,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:6502:20,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2249:11,  
    inlined from ‘testOperations’ at /workspace/boost/libs/beast/test/beast/core/static_string.cpp:590:26:  
/usr/include/c++/13/bits/char_traits.h:423:57: warning: ‘__builtin_memmove’ writing between 2 and 250 bytes into a region of size 0 overflows the destination [-Wstringop-overflow=]  
  423 |         return static_cast<char_type*>(__builtin_memmove(__s1, __s2, __n));  
      |                                                         ^  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp: In member function ‘testOperations’:  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp:589:34: note: at offset 9 into destination object ‘s2’ of size 8  
  589 |                 static_string<6> s2("12345");  
      |                                  ^  
In function ‘assign’,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:6503:22,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2249:11,  
    inlined from ‘testOperations’ at /workspace/boost/libs/beast/test/beast/core/static_string.cpp:590:26:  
/usr/include/c++/13/bits/char_traits.h:447:56: warning: ‘__builtin_memset’ writing 2 bytes into a region of size 1 overflows the destination [-Wstringop-overflow=]  
  447 |         return static_cast<char_type*>(__builtin_memset(__s, __a, __n));  
      |                                                        ^  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp: In member function ‘testOperations’:  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp:589:34: note: at offset 7 into destination object ‘s2’ of size 8  
  589 |                 static_string<6> s2("12345");  
      |                                  ^  
In function ‘move’,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:6536:20,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2316:11,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2281:18,  
    inlined from ‘testOperations’ at /workspace/boost/libs/beast/test/beast/core/static_string.cpp:616:26:  
/usr/include/c++/13/bits/char_traits.h:423:57: warning: ‘__builtin_memmove’ writing between 2 and 250 bytes into a region of size 0 overflows the destination [-Wstringop-overflow=]  
  423 |         return static_cast<char_type*>(__builtin_memmove(__s1, __s2, __n));  
      |                                                         ^  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp: In member function ‘testOperations’:  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp:615:34: note: at offset 9 into destination object ‘s2’ of size 8  
  615 |                 static_string<6> s2("12345");  
      |                                  ^  
In function ‘copy’,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:6549:24,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2316:11,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2281:18,  
    inlined from ‘testOperations’ at /workspace/boost/libs/beast/test/beast/core/static_string.cpp:616:26:  
/usr/include/c++/13/bits/char_traits.h:435:56: warning: ‘__builtin_memcpy’ writing 1 or more bytes into a region of size 0 overflows the destination [-Wstringop-overflow=]  
  435 |         return static_cast<char_type*>(__builtin_memcpy(__s1, __s2, __n));  
      |                                                        ^  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp: In member function ‘testOperations’:  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp:615:34: note: at offset 8 into destination object ‘s2’ of size 8  
  615 |                 static_string<6> s2("12345");  
      |                                  ^  
In function ‘copy’,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:6554:24,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2316:11,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2281:18,  
    inlined from ‘testOperations’ at /workspace/boost/libs/beast/test/beast/core/static_string.cpp:616:26:  
/usr/include/c++/13/bits/char_traits.h:435:56: warning: ‘__builtin_memcpy’ writing 2 bytes into a region of size 1 overflows the destination [-Wstringop-overflow=]  
  435 |         return static_cast<char_type*>(__builtin_memcpy(__s1, __s2, __n));  
      |                                                        ^  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp: In member function ‘testOperations’:  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp:615:34: note: at offset 7 into destination object ‘s2’ of size 8  
  615 |                 static_string<6> s2("12345");  
      |                                  ^  
In function ‘move’,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:6536:20,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2316:11,  
    inlined from ‘testOperations’ at /workspace/boost/libs/beast/test/beast/core/static_string.cpp:642:26:  
/usr/include/c++/13/bits/char_traits.h:423:57: warning: ‘__builtin_memmove’ writing between 2 and 250 bytes into a region of size 0 overflows the destination [-Wstringop-overflow=]  
  423 |         return static_cast<char_type*>(__builtin_memmove(__s1, __s2, __n));  
      |                                                         ^  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp: In member function ‘testOperations’:  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp:641:34: note: at offset 9 into destination object ‘s2’ of size 8  
  641 |                 static_string<6> s2("12345");  
      |                                  ^  
In function ‘copy’,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:6549:24,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2316:11,  
    inlined from ‘testOperations’ at /workspace/boost/libs/beast/test/beast/core/static_string.cpp:642:26:  
/usr/include/c++/13/bits/char_traits.h:435:56: warning: ‘__builtin_memcpy’ writing 1 or more bytes into a region of size 0 overflows the destination [-Wstringop-overflow=]  
  435 |         return static_cast<char_type*>(__builtin_memcpy(__s1, __s2, __n));  
      |                                                        ^  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp: In member function ‘testOperations’:  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp:641:34: note: at offset 8 into destination object ‘s2’ of size 8  
  641 |                 static_string<6> s2("12345");  
      |                                  ^  
In function ‘copy’,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:6554:24,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2316:11,  
    inlined from ‘testOperations’ at /workspace/boost/libs/beast/test/beast/core/static_string.cpp:642:26:  
/usr/include/c++/13/bits/char_traits.h:435:56: warning: ‘__builtin_memcpy’ writing 2 bytes into a region of size 1 overflows the destination [-Wstringop-overflow=]  
  435 |         return static_cast<char_type*>(__builtin_memcpy(__s1, __s2, __n));  
      |                                                        ^  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp: In member function ‘testOperations’:  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp:641:34: note: at offset 7 into destination object ‘s2’ of size 8  
  641 |                 static_string<6> s2("12345");  
      |                                  ^  
In function ‘move’,  
    inlined from ‘insert_unchecked’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:7009:20,  
    inlined from ‘insert_unchecked’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:5503:21,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2354:28,  
    inlined from ‘testOperations’ at /workspace/boost/libs/beast/test/beast/core/static_string.cpp:668:26:  
/usr/include/c++/13/bits/char_traits.h:423:57: warning: ‘__builtin_memmove’ writing between 2 and 250 bytes into a region of size 0 overflows the destination [-Wstringop-overflow=]  
  423 |         return static_cast<char_type*>(__builtin_memmove(__s1, __s2, __n));  
      |                                                         ^  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp: In member function ‘testOperations’:  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp:667:34: note: at offset 10 into destination object ‘s2’ of size 8  
  667 |                 static_string<6> s2("12345");  
      |                                  ^  
In function ‘copy’,  
    inlined from ‘insert_unchecked’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:7010:20,  
    inlined from ‘insert_unchecked’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:5503:21,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2354:28,  
    inlined from ‘testOperations’ at /workspace/boost/libs/beast/test/beast/core/static_string.cpp:668:26:  
/usr/include/c++/13/bits/char_traits.h:435:56: warning: ‘__builtin_memcpy’ writing 3 bytes into a region of size 1 overflows the destination [-Wstringop-overflow=]  
  435 |         return static_cast<char_type*>(__builtin_memcpy(__s1, __s2, __n));  
      |                                                        ^  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp: In member function ‘testOperations’:  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp:667:34: note: at offset 7 into destination object ‘s2’ of size 8  
  667 |                 static_string<6> s2("12345");  
      |                                  ^  
In function ‘move’,  
    inlined from ‘insert_unchecked’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:7009:20,  
    inlined from ‘insert_unchecked’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:5503:21,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2408:28,  
    inlined from ‘testOperations’ at /workspace/boost/libs/beast/test/beast/core/static_string.cpp:698:26:  
/usr/include/c++/13/bits/char_traits.h:423:57: warning: ‘__builtin_memmove’ writing between 2 and 250 bytes into a region of size 0 overflows the destination [-Wstringop-overflow=]  
  423 |         return static_cast<char_type*>(__builtin_memmove(__s1, __s2, __n));  
      |                                                         ^  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp: In member function ‘testOperations’:  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp:697:34: note: at offset 9 into destination object ‘s2’ of size 8  
  697 |                 static_string<6> s2("12345");  
      |                                  ^  
In function ‘copy’,  
    inlined from ‘insert_unchecked’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:7010:20,  
    inlined from ‘insert_unchecked’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:5503:21,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2408:28,  
    inlined from ‘testOperations’ at /workspace/boost/libs/beast/test/beast/core/static_string.cpp:698:26:  
/usr/include/c++/13/bits/char_traits.h:435:56: warning: ‘__builtin_memcpy’ writing 2 bytes into a region of size 1 overflows the destination [-Wstringop-overflow=]  
  435 |         return static_cast<char_type*>(__builtin_memcpy(__s1, __s2, __n));  
      |                                                        ^  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp: In member function ‘testOperations’:  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp:697:34: note: at offset 7 into destination object ‘s2’ of size 8  
  697 |                 static_string<6> s2("12345");  
      |                                  ^  
```

---

## Comment 1

> Username: gennaroprota  
> Created at: 2025-10-23 09:33:42 UTC  
> Url: https://github.com/boostorg/static_string/issues/74#issuecomment-3435966614  

I looked at the first warning. That's for:  
  
```  
static_string<6> s2("12345");  
s2.insert(6, "__");  
```  
  
which lands to:  
  
```  
BOOST_STATIC_STRING_CPP14_CONSTEXPR  
basic_static_string&  
insert(  
  size_type index,  
  const_pointer s,  
  size_type count)  
{  
  if (index > size())  
    detail::throw_exception<std::out_of_range>(  
      "index > size()");  
  insert(data() + index, s, s + count);  
  return *this;  
}  
```  
  
which should immediately throw a `std::out_of_range`. I suspect the warning comes from the lines after the `throw` (which aren't actually executed). Could you please try adding an `else` in your local copy?

---

## Comment 2

> Username: ashtum  
> Created at: 2025-10-23 11:35:48 UTC  
> Url: https://github.com/boostorg/static_string/issues/74#issuecomment-3436470490  

Adding an else branch didn't change it.  
It actually seems to be a link time warning which only happens in release builds:  
```CPP  
[ 41%] Building CXX object test/beast/core/CMakeFiles/boost_beast_tests_core.dir/static_string.cpp.o  
[ 41%] Linking CXX executable boost_beast_tests_core  
In function ‘assign’,  
    inlined from ‘copy_with_traits’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:307:19,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:6606:37,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2357:11,  
    inlined from ‘insert’ at /workspace/boost/libs/static_string/include/boost/static_string/static_string.hpp:2322:18,  
    inlined from ‘testOperations’ at /workspace/boost/libs/beast/test/beast/core/static_string.cpp:616:26:  
/usr/include/c++/14/bits/char_traits.h:350:14: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
  350 |         __c1 = __c2;  
      |              ^  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp: In member function ‘testOperations’:  
/workspace/boost/libs/beast/test/beast/core/static_string.cpp:615:34: note: at offset 8 into destination object ‘s2’ of size 8  
  615 |                 static_string<6> s2("12345");  
```

---

## Comment 3

> Username: gennaroprota  
> Created at: 2025-10-23 16:45:40 UTC  
> Url: https://github.com/boostorg/static_string/issues/74#issuecomment-3438048200  

Line 2357 is here:  
  
```  
BOOST_STATIC_STRING_CPP14_CONSTEXPR  
basic_static_string&  
insert(  
  size_type index,  
  const_pointer s,  
  size_type count)  
{  
  if (index > size())  
    detail::throw_exception<std::out_of_range>(  
      "index > size()");  
  insert(data() + index, s, s + count); // <--- line 2357  
  return *this;  
}  
```  
  
and that's not executed at all. So this looks like a false positive. Just to be sure: You changed that function to:  
  
```  
BOOST_STATIC_STRING_CPP14_CONSTEXPR  
basic_static_string&  
insert(  
  size_type index,  
  const_pointer s,  
  size_type count)  
{  
  if (index > size())  
    detail::throw_exception<std::out_of_range>(  
      "index > size()");  
  else  
    insert(data() + index, s, s + count);  
  return *this;  
}  
```  
  
and the warning remained?

---

## Comment 4

> Username: ashtum  
> Created at: 2025-10-24 10:22:33 UTC  
> Updated at: 2025-10-24 10:22:50 UTC  
> Url: https://github.com/boostorg/static_string/issues/74#issuecomment-3442380070  

> and that's not executed at all. So this looks like a false positive. Just to be sure: You changed that function to:  
> and the warning remained?  
  
Correct, it didn't make any difference, I'm pretty sure this is a linker bug because it only happens when the generated object file exceeds a certain threshold of complexity,

---

## Comment 5

> Username: gennaroprota  
> Created at: 2025-10-24 14:53:22 UTC  
> Url: https://github.com/boostorg/static_string/issues/74#issuecomment-3443587346  

Another question: Which version of GCC are you using? static_string.hpp disables `-Wstringop-overflow` warnings for GCC >= 8.

---

## Comment 6

> Username: gennaroprota  
> Created at: 2025-10-29 17:53:43 UTC  
> Url: https://github.com/boostorg/static_string/issues/74#issuecomment-3462874919  

Upon further investigation, this appears to be a linker bug. Although StaticString has a pragma to suppress the warning, it has no effect during link-time optimization, when the warning is actually triggered. And since StaticString is header-only, the issue must be addressed in the build flags of the consuming code.
