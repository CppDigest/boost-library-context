# #254 - Console colors are not restored on Windows [Closed]

> Username: valiko-ua  
> Created at: 2020-02-23 21:32:42 UTC  
> Updated at: 2020-04-21 14:23:40 UTC  
> Closed at: 2020-04-06 12:21:48 UTC  
> Url: https://github.com/boostorg/test/issues/254  

Failing tests set color to red and do not restore (on Windows), so that all subsequent output is red.  
  
Program to reproduce:  
  
```c++  
#define BOOST_TEST_MODULE My Test  
#include <boost/test/included/unit_test.hpp>  
BOOST_AUTO_TEST_CASE(my_test)  
{  
    int i = 1;  
    BOOST_TEST(i == 2);  
    BOOST_TEST(i == 3);  
}  
```  
![boost_test_bug_win](https://user-images.githubusercontent.com/13869060/75120442-baf52d00-5694-11ea-8c6d-3684e1afcf34.png)

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2020-04-03 13:09:59 UTC  
> Url: https://github.com/boostorg/test/issues/254#issuecomment-608424101  

Fix on branch `topic/GH-254-color-restored-windows`. Would be good if you give a try.

---

## Comment 2

> Username: valiko-ua  
> Created at: 2020-04-03 20:25:09 UTC  
> Url: https://github.com/boostorg/test/issues/254#issuecomment-608641956  

I tested on Windows: commit https://github.com/boostorg/test/commit/63ab1f2bd67bb80ba8512a560fd42065295151c0 fixes this bug. Colors are restored.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2020-04-06 12:22:05 UTC  
> Url: https://github.com/boostorg/test/issues/254#issuecomment-609760845  

Fixed in master
