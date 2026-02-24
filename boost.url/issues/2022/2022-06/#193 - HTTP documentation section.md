# #193 - HTTP documentation section [Open]

> Username: vinniefalco  
> Created at: 2022-06-08 16:28:48 UTC  
> Updated at: 2023-12-19 04:00:58 UTC  
> Url: https://github.com/boostorg/url/issues/193  

The documentation should have a section dedicated to thoroughly explaining the production and consumption of URLs used in the HTTP request-target, as per RFC7230 (and errata):  
  
https://datatracker.ietf.org/doc/html/rfc7230#section-3.1.1

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-08 16:30:03 UTC  
> Url: https://github.com/boostorg/url/issues/193#issuecomment-1150137860  

This Javadoc needs additional explanation:  
https://master.url.cpp.al/url/ref/boost__urls__url_view/is_path_absolute.html  
  
See: https://github.com/CPPAlliance/url/issues/191#issuecomment-1150137455

---

## Comment 2

> Username: ecorm  
> Created at: 2023-12-19 01:11:07 UTC  
> Url: https://github.com/boostorg/url/issues/193#issuecomment-1861939977  

For an HTTP server parsing the request-target, is it sufficient to call [parse_uri_reference](https://www.boost.org/doc/libs/release/libs/url/doc/html/url/ref/boost__urls__parse_uri_reference.html) once, and then use the various `url_view::has_*` methods to verify that the URL belongs to one of the `origin-form`, `absolute-form`, `authority-form`, or `asterisk-form` [formats](https://datatracker.ietf.org/doc/html/rfc7230#section-5.3), depending on the HTTP verb?

---

## Comment 3

> Username: ecorm  
> Created at: 2023-12-19 03:56:34 UTC  
> Updated at: 2023-12-19 04:00:58 UTC  
> Url: https://github.com/boostorg/url/issues/193#issuecomment-1862076898  

In the absence of the docs proposed in this issue, I have cobbled together some functions to validate a request-target URL within an HTTP server. If you don't mind, please take a quick look and let me know if I'm on the right path. Perhaps my naive approach would inspire you to write a better example in the documentation.  
  
```c++  
boost::optional<  
    boost::beast::http::request_parser<  
        boost::beast::http::string_body>> parser_;  
  
bool parseAndNormalizeTarget()  
{  
    auto url = boost::urls::parse_uri_reference(parser_->get().target());  
    if (url.has_error())  
        return false;  
      
    target_ = *url;  
    target_.normalize();  
    return urlIsValid(target_, parser_->get().method());  
}  
  
static bool urlIsValid(const boost::urls::url& url,  
                       boost::beast::http::verb verb)  
{  
    using V = boost::beast::http::verb;  
  
    switch (verb)  
    {  
    case V::delete_: case V::get: case V::head:  
    case V::post: case V::put: case V::trace:  
        return isOriginFormUrl(url) ||  // RFC7230, section 5.3.1  
               isAbsoluteFormUrl(url);  // RFC7230, section 5.3.2  
  
    case V::connect:  
        return isAuthorityFormUrl(url); // RFC7230, section 5.3.3  
  
    case V::options:  
        return isAsteriskFormUrl(url);  // RFC7230, section 5.3.4  
  
    default:  
        break;  
    }  
  
    return true; // Caller needs to check for other, non-standard verbs.  
}  
  
static bool isOriginFormUrl(const boost::urls::url& url)  
{  
    /**  
    origin-form   = absolute-path [ "?" query ]  
    absolute-path = 1*( "/" segment )  
    */  
    return !url.has_scheme() &&  
           !url.has_authority() &&  
           url.is_path_absolute();  
}  
  
static bool isAbsoluteFormUrl(const boost::urls::url& url)  
{  
    /**  
    absolute-form = absolute-URI ; defers to RFC3986  
    absolute-URI  = scheme ":" hier-part [ "?" query ]  
    hier-part     = "//" authority path-abempty  
                     / path-absolute  
                     / path-rootless  
                     / path-empty  
    path-abempty  = *( "/" segment ) ; begins with "/" or is empty  
    path-absolute = "/" [ segment-nz *( "/" segment ) ] ; begins with "/"  
                                                          but not "//"  
    path-rootless = segment-nz *( "/" segment ) ; begins with a segment  
    path-empty    = 0<pchar> ; zero characters  
  
    // Initial logic expression:  
    bool pabs = url.is_path_absolute();  
    bool pempty = url.path().empty();  
    bool is_absolute_form =  
        url.has_scheme() &&  
        (url.has_authority()  
            ? (pabs || pempty)      // path-abempty  
            : (pabs ||              // path-absolute  
              (!pabs && !pempty) || // path-rootless  
               pempty));            // path-empty  
  
    // Simplifies to:  
    bool is_absolute_form =  
        url.has_scheme() &&  
        (url.has_authority()  
             ? true  // Path can only be absolute or empty with an authority  
             : true); // Logical tautology  
  
    // Which simplifies to:  
    bool is_absolute_form = url.has_scheme();  
  
    Proof of logical tautology:  
        pabs || (!pabs && !pempty) || pempty  
    Rearranging the OR terms and grouping:  
        (pabs || pempty) || (!pabs && !pempty)  
    Applying De Morgan's Theorem to the last OR term:  
        (pabs || pempty) || !(pabs || pempty)  
    Substituting A = (pabs || pempty):  
        A || !A  
    which is always true.  
    */  
  
    return url.has_scheme();  
}  
  
static bool isAuthorityFormUrl(const boost::urls::url& url)  
{  
    /**  
    authority-form = authority ; defers to RFC3986  
    authority      = [ userinfo "@" ] host [ ":" port ]  
    */  
    return !url.has_scheme() && url.has_authority() && url.path().empty();  
}  
  
static bool isAsteriskFormUrl(const boost::urls::url& url)  
{  
    // asterisk-form = "*"  
    return url.buffer() == "*";  
}  
```
