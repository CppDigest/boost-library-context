# #462 - explore bidirectional in query parameters [Closed]

> Username: vinniefalco  
> Created at: 2022-08-27 00:01:46 UTC  
> Updated at: 2022-09-02 17:22:21 UTC  
> Closed at: 2022-09-02 17:22:20 UTC  
> Url: https://github.com/boostorg/url/issues/462  

We could offer Bidirectional in query parameters. This allows `find_prev`, and erasure algorithms which run in reverse.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-27 00:02:12 UTC  
> Url: https://github.com/boostorg/url/issues/462#issuecomment-1229050083  

```  
    /** Return an iterator to the previous matching element  
  
        This function searches for the key  
        starting at the element prior to  
        `before` and continuing until either  
        the key is found, or the beginning of  
        the range is reached in which case  
        `end()` is returned.  
  
        @par Example  
        @code  
        url u( "?First=John&Last=Doe" );  
  
        assert( u.params().find_prev( u.params().end(), "Last" ) -> value == "Doe" );  
        @endcode  
  
        @par Complexity  
        Linear in the size of the referenced url.  
  
        @return an iterator to the element  
  
        @param before The element before which  
            the search is started. This can  
            be `end()`.  
  
        @param key The key to match. A  
        case-sensitive comparison is used  
        by default.  
  
        @param ic An optional parameter. If  
            the value @ref ignore_case is  
            passed here, the comparison will  
            be case-insensitive.  
    */  
    BOOST_URL_DECL  
    iterator  
    find_prev(  
        iterator before,  
        string_view key,  
        ignore_case_param ic = {}) const noexcept;  
```

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-27 00:05:10 UTC  
> Url: https://github.com/boostorg/url/issues/462#issuecomment-1229051128  

https://github.com/alandefreitas/url/tree/archived/bidirectional_params

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-02 17:22:20 UTC  
> Url: https://github.com/boostorg/url/issues/462#issuecomment-1235742849  

Done
