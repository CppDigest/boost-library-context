# #586 - decoded_size [Closed]

> Username: vinniefalco  
> Created at: 2022-10-10 01:30:33 UTC  
> Updated at: 2022-10-10 17:27:58 UTC  
> Closed at: 2022-10-10 17:27:58 UTC  
> Url: https://github.com/boostorg/url/issues/586  

Somehow `decoded_size` got lost in the kerfuffle. I guess because `pct_string_view` already knows it, but we still need this signature:  
```  
std::size_t  
decoded_size( string_view s ) noexcept;  
```  
  
or perhaps this one  
```  
result< std::size_t >  
decoded_size( string_view s ) noexcept;  
```

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-10-10 17:24:55 UTC  
> Url: https://github.com/boostorg/url/issues/586#issuecomment-1273619429  

I think the rationale for removing the public function was that one could create a `pct_string_view` and get the decoded size.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-10-10 17:27:58 UTC  
> Url: https://github.com/boostorg/url/issues/586#issuecomment-1273622254  

Yes
