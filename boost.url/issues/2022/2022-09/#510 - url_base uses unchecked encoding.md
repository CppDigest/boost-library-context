# #510 - url_base uses unchecked encoding [Closed]

> Username: vinniefalco  
> Created at: 2022-09-07 00:03:40 UTC  
> Updated at: 2022-09-27 17:27:11 UTC  
> Closed at: 2022-09-27 17:27:11 UTC  
> Url: https://github.com/boostorg/url/issues/510  

`url_base` should use unchecked algorithms to write to the destination since we already know the size

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-09-26 23:16:17 UTC  
> Url: https://github.com/boostorg/url/issues/510#issuecomment-1258754135  

I think the decoded size is already being used. For instance,  
  
```cpp  
template<BOOST_URL_STRTOK_TPARAM>  
    BOOST_URL_STRTOK_RETURN  
    host(  
        BOOST_URL_STRTOK_ARG(token)) const  
    {  
        decode_opts opt;  
        opt.plus_to_space = false;  
        return encoded_host().decode(  
            opt, std::move(token));  
    }  
```  
  
uses `encoded_host()`, which already knows its size:  
  
```cpp  
pct_string_view  
url_view_base::  
encoded_host() const noexcept  
{  
    return detail::make_pct_string_view(  
        pi_->get(id_host),  
        pi_->decoded_[id_host]);  
}  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-27 15:32:48 UTC  
> Url: https://github.com/boostorg/url/issues/510#issuecomment-1259680698  

Great!
