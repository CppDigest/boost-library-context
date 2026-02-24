# #646 - False posititive in URL comparison [Closed]

> Username: alandefreitas  
> Created at: 2022-12-22 17:12:12 UTC  
> Updated at: 2022-12-22 20:11:35 UTC  
> Closed at: 2022-12-22 20:11:35 UTC  
> Url: https://github.com/boostorg/url/issues/646  

The `compare` function would compare an absent component and an empty component as the same thing, but that's wrong.  
  
```cpp  
BOOST_TEST_EQ(url("https:"), url("https://"));  
BOOST_TEST_EQ(url("https://@www.boost.org"), url("https://www.boost.org"));  
BOOST_TEST_EQ(url("https:"), url("https:?"));  
BOOST_TEST_EQ(url("https:"), url("https:#"));  
// ... etc  
```  
  
should fail.
