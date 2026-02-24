# #253 - source location for grammar/rfc errors [Closed]

> Username: vinniefalco  
> Created at: 2022-07-27 15:40:16 UTC  
> Updated at: 2022-09-14 00:56:12 UTC  
> Closed at: 2022-09-14 00:56:12 UTC  
> Url: https://github.com/boostorg/url/issues/253  

Lines which return error codes should incorporate the source location:  
```  
# define BOOST_URL_RETURN( ev ) { \  
    static constexpr auto loc( BOOST_CURRENT_LOCATION ); \  
    return ::boost::system::error_code( (ev), &loc ); }  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-14 00:56:12 UTC  
> Url: https://github.com/boostorg/url/issues/253#issuecomment-1246101178  

Done, grammar is complete
