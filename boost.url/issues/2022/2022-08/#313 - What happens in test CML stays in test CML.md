# #313 - What happens in test CML stays in test CML [Closed]

> Username: vinniefalco  
> Created at: 2022-08-03 13:38:40 UTC  
> Updated at: 2022-08-03 22:56:59 UTC  
> Closed at: 2022-08-03 22:56:59 UTC  
> Url: https://github.com/boostorg/url/issues/313  

All of the information required to build the unit tests should be in the unit test CML and not spread around where I have to humiliate myself by asking other people how my own project is built:  
https://github.com/CPPAlliance/url/blob/a3447a91c46b0e98f9542c8baeae76fb431817cf/CMakeLists.txt#L53

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-03 17:10:28 UTC  
> Url: https://github.com/boostorg/url/issues/313#issuecomment-1204243881  

I split this into  
  
```  
    set(BOOST_URL_UNIT_TEST_LIBRARIES container filesystem)  
    set(BOOST_INCLUDE_LIBRARIES url json ${BOOST_URL_UNIT_TEST_LIBRARIES})  
```  
  
so that it's cleaner than  
  
```  
    set(BOOST_INCLUDE_LIBRARIES url json container filesystem)  
```  
  
which is what was happening before.  
  
> should be in the unit test CML  
  
I don't really think that's possible because we would have to `find_package` or `add_subdirectory(../..)` again, which doesn't work as far as I know.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-03 17:12:01 UTC  
> Url: https://github.com/boostorg/url/issues/313#issuecomment-1204245694  

maybe leave a comment in the test CML explaining that the include dependencies are in the CML that is one directory up?
