# #312 - This compiles: `BOOST_TEST(1 = somestring.size());` [Closed]

> Username: zethon  
> Created at: 2021-07-08 22:26:43 UTC  
> Updated at: 2021-07-21 11:22:11 UTC  
> Closed at: 2021-07-08 23:37:40 UTC  
> Url: https://github.com/boostorg/test/issues/312  

The following code compiles when using unit_test.hpp  
  
```  
BOOST_TEST(1 = somestring.size());  
```  
  
The code does fail to compile when using lightweight_test.hpp.  
  
See: https://gcc.godbolt.org/z/MT8xrdesh

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2021-07-08 23:37:37 UTC  
> Url: https://github.com/boostorg/test/issues/312#issuecomment-876810225  

`lightweight_test.hpp` is not part of Boost.Test.

---

## Comment 2

> Username: zethon  
> Created at: 2021-07-09 00:44:17 UTC  
> Url: https://github.com/boostorg/test/issues/312#issuecomment-876832334  

> lightweight_test.hpp is not part of Boost.Test.  
  
That's fine, but the bug is in unit_test.hpp. The could I pasted **should not** compile.

---

## Comment 3

> Username: zethon  
> Created at: 2021-07-21 11:22:11 UTC  
> Url: https://github.com/boostorg/test/issues/312#issuecomment-884113899  

*bump* ?
