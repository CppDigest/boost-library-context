# #399 - Retrieve the Target of a URL suitable for sending in an HTTP Request [Closed]

> Username: madmongo1  
> Created at: 2022-08-13 17:20:36 UTC  
> Updated at: 2022-08-17 07:23:34 UTC  
> Closed at: 2022-08-17 07:23:34 UTC  
> Url: https://github.com/boostorg/url/issues/399  

There is not ez-mode way to extract the HTTP Target from a `url` or `url_view`. I believe that this would be a common operation in real-world use. I think the library would benefit from a function to provide this.  
  
Use case:  
  
```  
    boost::beast::websocket::response_type response;  
    co_await ws.async_handshake(response, url.encoded_hostname(), target(url), use_awaitable);  
```  
  
My attempt at an implementation  
```  
/// Return the target form an HTTP URL suitable for specifying as the target in  
/// an http request  
std::string  
target(boost::urls::url u)  
{  
    u.remove_scheme().remove_authority().remove_userinfo();  
    if (!u.is_path_absolute())  
        throw std::runtime_error(fmt::format("path not absolute: {}", u.string()));  
    auto sv = u.string();  
    return std::string(sv.begin(), sv.end());  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-13 17:29:53 UTC  
> Url: https://github.com/boostorg/url/issues/399#issuecomment-1214194097  

This seems HTTP-specific... I think it is the responsibility of the user

---

## Comment 2

> Username: madmongo1  
> Created at: 2022-08-13 17:42:39 UTC  
> Url: https://github.com/boostorg/url/issues/399#issuecomment-1214195906  

My unfounded assertion: This is going to be a huge source of support calls and user frustration. Users don't read RFCs.  
My compromise: show it as an example that users can copy/paste.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-13 18:04:35 UTC  
> Updated at: 2022-08-13 18:04:45 UTC  
> Url: https://github.com/boostorg/url/issues/399#issuecomment-1214199316  

We could offer  
```  
string_view  
url_view_base::  
encoded_target() const noexcept  
{  
    return get( id_path, id_frag );  
}  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-08-13 18:05:56 UTC  
> Url: https://github.com/boostorg/url/issues/399#issuecomment-1214199524  

Try   
```  
urls::result< urls::url_view > = urls::parse_origin_form( s );  
```

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-08-16 02:02:17 UTC  
> Url: https://github.com/boostorg/url/issues/399#issuecomment-1216059064  

The term `target` is kind of problematic because it can be more than `origin_form`.  
  
```  
request-target = origin-form  
                    / absolute-form  
                    / authority-form  
                    / asterisk-form  
```  
  
and   
  
```  
urls::result< urls::url_view > = urls::parse_origin_form( s );  
```  
  
already exists.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-08-16 02:25:51 UTC  
> Url: https://github.com/boostorg/url/issues/399#issuecomment-1216071368  

URI.js calls path+query+fragment the `resource` (we would call it `encoded_resource`). But this includes the fragment, which we don't want.  
  
I think "target" is fine. "request-target" is not the same as "target." In fact target doesn't have any RFC meaning either in 3986 or 7230 so I think its okay. `encoded_target` for path+query, and `encoded_resource` for path+query+fragment.
