# #245 - fix set_query [Closed]

> Username: alandefreitas  
> Created at: 2022-07-25 23:24:12 UTC  
> Updated at: 2022-08-03 23:09:36 UTC  
> Closed at: 2022-08-03 23:09:36 UTC  
> Url: https://github.com/boostorg/url/issues/245  

This fails:  
  
```cpp  
url u;  
u.set_query("");  
u.set_query("");  
BOOST_TEST_EQ(u.string(), "?");  
```
