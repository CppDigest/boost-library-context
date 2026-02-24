# #844 - Fix urls::errc reference [Closed]

> Username: alandefreitas  
> Created at: 2024-05-30 18:01:51 UTC  
> Updated at: 2024-08-20 00:48:55 UTC  
> Closed at: 2024-08-20 00:48:55 UTC  
> Url: https://github.com/boostorg/url/issues/844  

`errc` is documented as:  
  
```  
#ifdef BOOST_URL_DOCS  
enum errc  
{  
    __see_below__  
};  
#else  
namespace errc = boost::system::errc;  
#endif  
```  
  
It should always be `namespace errc = boost::system::errc;` and, since it's been deprecated for a few releases already, we must remove it from the index.
