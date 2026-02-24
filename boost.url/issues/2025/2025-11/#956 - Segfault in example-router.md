# #956 - Segfault in example/router [Closed]

> Username: dmtai  
> Created at: 2025-11-27 18:45:01 UTC  
> Updated at: 2026-01-20 20:52:02 UTC  
> Closed at: 2026-01-20 20:52:02 UTC  
> Url: https://github.com/boostorg/url/issues/956  

For example:  
  
```  
auto tmp_r = new urls::router<handler>;  
urls::router<handler> r = *tmp_r; // copy impl_ pointer from router_base  
delete tmp_r; // delete impl_ pointer  
  
r.insert("/", [&](connection& c, urls::matches const&) { // segfault  
    c.string_reply("Hello!");  
});  
```
