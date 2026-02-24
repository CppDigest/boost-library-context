# #580 - make_url [Closed]

> Username: vinniefalco  
> Created at: 2022-10-04 15:00:10 UTC  
> Updated at: 2022-10-21 17:21:18 UTC  
> Closed at: 2022-10-21 17:21:18 UTC  
> Url: https://github.com/boostorg/url/issues/580  

We could get rid of the "fluent" syntax since it has problems with the base class, and replace the missing functionality with this:  
  
```  
namespace detail {  
  
struct make_url_none  
{  
    std::size_t  
    measure() const noexcept  
    {  
        return 0;  
    }  
  
    void  
    copy(url_base&) const noexcept  
    {  
    }  
};  
  
struct make_url_scheme_id  
{  
    scheme id;  
  
    std::size_t  
    measure() const noexcept  
    {  
        return to_string(id).size() + 1;  
    }  
  
    void  
    copy(url_base& u) const noexcept  
    {  
        u.set_scheme_id(id);  
    }  
};  
  
struct make_url_scheme_string  
{  
    string_view s;  
  
    std::size_t  
    measure() const noexcept  
    {  
        return s.size() + 1;  
    }  
  
    void  
    copy(url_base& u) const noexcept  
    {  
        u.set_scheme(s);  
    }  
};  
  
inline  
auto  
make_url_scheme(  
    std::nullptr_t) noexcept ->  
        make_url_none  
{  
    return {};  
}  
  
inline  
auto  
make_url_scheme(  
    scheme id) noexcept ->  
        make_url_scheme_id  
{  
    return make_url_scheme_id{id};  
}  
  
inline  
auto  
make_url_scheme(  
    string_view s) noexcept ->  
        make_url_scheme_string  
{  
    return make_url_scheme_string{s};  
}  
  
inline  
auto  
make_url_authority(  
    std::nullptr_t) noexcept ->  
        make_url_none  
{  
    return {};  
}  
  
inline  
auto  
make_url_path(  
    std::nullptr_t) noexcept ->  
        make_url_none  
{  
    return {};  
}  
  
inline  
auto  
make_url_query(  
    std::nullptr_t) noexcept ->  
        make_url_none  
{  
    return {};  
}  
  
inline  
auto  
make_url_fragment(  
    std::nullptr_t) noexcept ->  
        make_url_none  
{  
    return {};  
}  
  
} //  
  
template<  
    class Scheme,  
    class Authority,  
    class Path,  
    class Query,  
    class Fragment>  
url  
make_url(  
    Scheme const& scheme,  
    Authority const& authority,  
    Path const& path,  
    Query const& query,  
    Fragment const& fragment)  
{  
    auto p0 = detail::make_url_scheme(scheme);  
    auto p1 = detail::make_url_authority(authority);  
    auto p2 = detail::make_url_path(path);  
    auto p3 = detail::make_url_query(query);  
    auto p4 = detail::make_url_fragment(fragment);  
  
    auto n = 0;  
    n += p0.measure();  
    n += p1.measure();  
    n += p2.measure();  
    n += p3.measure();  
    n += p4.measure();  
  
    url u;  
    u.reserve(n);  
  
    p0.copy(u);  
    p1.copy(u);  
    p2.copy(u);  
    p3.copy(u);  
    p4.copy(u);  
  
    return u;  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-10-04 23:37:17 UTC  
> Url: https://github.com/boostorg/url/issues/580#issuecomment-1267723494  

if we get rid of the fluent syntax it will wreck all the examples and javadocs:  
```  
assert( url("?first=john&last=doe" ).set_encoded_fragment( "john doe" ).encoded_fragment() == "john%20doe" );  
```
