# #401 - resolve ref to base into base [Closed]

> Username: madmongo1  
> Created at: 2022-08-13 17:34:37 UTC  
> Updated at: 2022-09-07 22:29:39 UTC  
> Closed at: 2022-09-07 22:29:39 UTC  
> Url: https://github.com/boostorg/url/issues/401  

The following code:  
  
```cpp  
    boost::urls::resolve(url, loc, url, ec);  
```  
  
Asserts, because the base and dest are the same object.   
When performing a client redirect, I would prefer to avoid writing this code:  
  
```cpp  
  auto new_url = boost::urls::url();  
  boost::urls::redirect(my_url, new_loc, new_url, ec);  
  if (!ec) {  
    my_url = std::move(new_url);  // feels redundant. Can't the library do this for me?  
    goto do_redirect;  
  }  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-13 17:36:56 UTC  
> Updated at: 2022-08-13 17:38:26 UTC  
> Url: https://github.com/boostorg/url/issues/401#issuecomment-1214195051  

We could provide this overload  
```  
void resolve( url_base& base, url_view_base const& ref, error_code& ec );  
```  
  
Actually this could be a member function of `url_base`  
```  
void url_base::resolve( url_view_base const& ref, error_code& ec );  
```

---

## Comment 2

> Username: madmongo1  
> Created at: 2022-08-13 17:40:22 UTC  
> Url: https://github.com/boostorg/url/issues/401#issuecomment-1214195565  

This is my workaround:  
  
Call site:  
```  
                    // perform the redirect by updating the URL and jumping to  
                    // the goto label above.  
                    url = resolve_redirect(  
                        url,  
                        boost::urls::url_view(  
                            response[beast::http::field::location]));  
                    goto again;  
```  
  
implementation:  
```  
boost::urls::url  
resolve_redirect(boost::urls::url_view original, boost::urls::url_view loc)  
{  
    auto result = boost::urls::url();  
    auto ec     = error_code();  
    boost::urls::resolve(original, loc, result, ec);  
    if (ec)  
        throw system_error(ec, "failed to resolve redirect Location");  
    return result;  
}  
```
