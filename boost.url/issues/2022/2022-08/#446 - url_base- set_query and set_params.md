# #446 - url_base: set_query and set_params [Closed]

> Username: alandefreitas  
> Created at: 2022-08-22 20:53:41 UTC  
> Updated at: 2023-07-18 21:51:57 UTC  
> Closed at: 2023-07-18 21:51:57 UTC  
> Url: https://github.com/boostorg/url/issues/446  

> One of the more common scenarios in our applications is the construction  
> of urls after the user provided some login credentials and often  
> hosts/port combinations. Also a prefix path is often needed (for example  
> to differentiate between a demo or beta service and the real production  
> api). I feel like the current  API design lacks a bit in this regard.  
>   
> To construct a new url i did the following:  
>   
>      auto url = boost::urls::url{};  
>      url.set_scheme("https")  
>       .set_host("[special-api.com](http://special-api.com/)")  
>       .set_port("443")  
>       .set_path("/prefix/api/v1/");  
>      url.segments().push_back("applications");  
>      url.segments().push_back(applicationId);  
>      url.segments().push_back("devices");  
>      url.params().append("since", "2022-01-01");  
>   
> The url::set_... methods seem to follow a fluent api design which is  
> nice, but unfortunately they return url_base&. So I can't construct a  
> url inside a function call like:  
>   
> httpClientGet(url{}.set_host(host).set_path("some/path"))  
>   
> Being able to add segments is great. Personally I'm a big fan of  
> overloading the /-operator to append paths like in the filesystem  
> library, because I think this results in more readable code. The example  
> of above could become:  
>   
> httpClientGet(url{host} / "some/path")

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-11 23:40:47 UTC  
> Url: https://github.com/boostorg/url/issues/446#issuecomment-1243069423  

We can't really do anything about this. The API tries to be as fluent as possible, but when it conflicts with a standard function like `push_back` or `append`, we prioritize established practice over fluency.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-11 23:45:37 UTC  
> Url: https://github.com/boostorg/url/issues/446#issuecomment-1243070410  

We could do something like this  
```  
void  
set_url_from_parts(  
    url_base& u,  
    scheme id,  
    pct_string_view authority,  
    pct_string_view path,  
    pct_string_view query,  
    pct_string_view fragment);  
```  
  
Or we could do something along these lines:  
```  
void  
set_url_from_parts(  
    url_base& u,  
    scheme_ref scheme,  
    host_ref host,  
    unsigned short port, // 0 = none  
    std::initializer_list<string_view> path,  
    std::initializer_list<param_view> query,  
    string_view fragment);  
  
struct scheme_ref  
{  
    scheme id;  
    string_view text;  
  
    scheme_ref(scheme id_) noexcept  
        : id(id_)  
    {  
    }  
  
    scheme_ref(string_view s) noexcept  
        : id(string_to_scheme(s))  
        , text(s)  
    {  
    }  
};  
  
struct host_ref; // etc...  
```

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-09-12 23:44:46 UTC  
> Updated at: 2022-09-12 23:45:16 UTC  
> Url: https://github.com/boostorg/url/issues/446#issuecomment-1244717679  

I'm not sure there's any solution that's going to be a lot better than the snippet included in the review. The user is doing a lot there. I have the impression that most solutions are almost just as verbose at the cost of being much more confusing:  
  
Original snippet:  
  
```cpp  
auto url = boost::urls::url{};  
 url.set_scheme("https")  
  .set_host("[special-api.com](http://special-api.com/)")  
  .set_port("443")  
  .set_path("/prefix/api/v1/");  
 url.segments().push_back("applications");  
 url.segments().push_back(applicationId);  
 url.segments().push_back("devices");  
 url.params().append("since", "2022-01-01");  
```  
  
Aggregating the constants:  
  
```cpp  
boost::urls::url u("https://special-api.com:443/prefix/api/v1/applications?since=2022-01-01");  
url.segments().append({applicationId, "devices"});  
```  
  
`<format>`:  
  
```cpp  
boost::urls::url u(  
    std::format(  
        "https://special-api.com:443/prefix/api/v1/applications/{}/devices?since=2022-01-01",   
        applicationId));  
```  
  
Snippet set_url_from_parts:  
  
```cpp  
auto url = boost::urls::url{};  
url.set_url_from_parts(  
    "",                                // when do we need the base here?  
    "https",                          
    "special-api.com",  
    443,  
    {"prefix", "api", "v1", "applications", applicationId, "devices"}, // how do we set absolute/relative paths?  
    {{"since", "2022-01-01"}},              // urls::no_value for no query?  
    none);                                           // urls::no_value for no fragment?  
```  
  
Besides the verbosity of this individual case, the biggest problem is the reviewer was making a case against returning `url_base&` here:   
  
> "The url::set_... methods seem to follow a fluent api design which is nice, but unfortunately they return url_base&.".   
  
In that case, not considering the possibility of returning `url&` there, we would still need overloads for `set_path` and `set_params` to achieve the fluent API the user wanted:  
  
```cpp  
auto url =   
boost::urls::url("https://special-api.com:443")  
  .set_segments({"prefix", "api", "v1", "applications", applicationId, "devices"})  
  .set_params({{"since", "2022-01-01"}});  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-09-13 13:44:12 UTC  
> Url: https://github.com/boostorg/url/issues/446#issuecomment-1245435258  

`set_segments` and `set_params` are nice :)

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-09-13 17:44:54 UTC  
> Url: https://github.com/boostorg/url/issues/446#issuecomment-1245739576  

Yep. It probably fixes the problem for that reviewer.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-09-13 19:00:29 UTC  
> Url: https://github.com/boostorg/url/issues/446#issuecomment-1245838354  

we can add set_segments and set_params in a future version

---

## Comment 7

> Username: alandefreitas  
> Created at: 2022-10-05 20:58:28 UTC  
> Url: https://github.com/boostorg/url/issues/446#issuecomment-1268968343  

If the fluent API no longer exists, `set_segments` and `set_params` are not very useful because we can always  
  
```cpp  
u.encoded_segments() = { "path", "to%3F", "file#" };  
```

---

## Comment 8

> Username: alandefreitas  
> Created at: 2022-10-20 23:52:09 UTC  
> Url: https://github.com/boostorg/url/issues/446#issuecomment-1286285386  

I think this has fixed itself with `u.encoded_segments() = { "path", "to%3F", "file#" };`
