# #476 - set_query, set_encoded_query incorrect for empty string [Closed]

> Username: vinniefalco  
> Created at: 2022-08-30 02:30:50 UTC  
> Updated at: 2022-09-02 20:31:28 UTC  
> Closed at: 2022-09-02 20:31:28 UTC  
> Url: https://github.com/boostorg/url/issues/476  

When called with an empty string, setting the query actually clears it:  
https://github.com/CPPAlliance/url/blob/afb91bc384168ed912e991ae436b8e8bbbde7d89/include/boost/url/impl/url_base.ipp#L1629

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-30 02:31:57 UTC  
> Url: https://github.com/boostorg/url/issues/476#issuecomment-1231070335  

The javadoc is correct:  
```  
        is thrown. When this function returns,  
        @ref has_query will return `true`.  
```  
  
but the behavior of the function does not match.
