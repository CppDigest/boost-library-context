# #171 - basic_headers, Headers to become basic_fields, Fields (API Change) [Closed]

> Username: vinniefalco  
> Created at: 2016-11-07 16:47:09 UTC  
> Updated at: 2016-11-15 22:28:06 UTC  
> Closed at: 2016-11-15 22:28:06 UTC  
> Url: https://github.com/boostorg/beast/issues/171  

Now that we have `message_headers` to refer to a HTTP message without the body, the term "headers" has become ambiguous. Specifically `class basic_headers`, and the `Headers` template argument name can be confused with `message_headers`. I am strongly considering the following changes:  
  
* Define "fields" to mean to a collection of HTTP field name / value pairs  
* Define "header" is the Start Line plus Header Fields (or just "fields")  
* Rename class template `basic_headers` to `basic_fields`  
* Rename class template alias `headers` to `fields`  
* Rename **`Headers`** in template parameter lists to **`Fields`**  
* Rename `message_headers` to just `header`  
* Rename `message_headers::headers` to `header::fields`  
* Change documentation to be consistent with these terms  
  
Synopsis of proposed changes  
```  
namespace beast {  
namespace http {  
  
/// A container for HTTP field value pairs  
template<class Allocator>  
class basic_fields  
{  
    ...  
};  
  
/// A container for HTTP request or response headers  
template<bool isRequest, class Fields>  
struct header  
{  
    using fields_type = Fields;  
  
    int version;  
    std::string method;     // request  
    std::string uri;        // request  
    int status;             // response  
    std::string reason;     // response  
  
    fields_type fields;  
};  
  
/// A container for a complete HTTP message  
template<bool isRequest, class Body, class Fields>  
struct message : header<isRequest, Fields>  
{  
    ...  
};  
  
/// A typical HTTP field value pair container  
using fields = basic_fields<std::allocator<char>>;  
  
/// Typical HTTP request headers  
template<class Fields = fields>  
using request_header = header<true, Fields>;  
  
/// Typical HTTP response headers  
template<class Fields = fields>  
using response_header = header<false, Fields>;  
  
struct none  
{  
#if GENERATING_DOCS  
    /// The type of the `message::body` member  
    using value_type = void;  
#else  
    struct value_type {};  
#endif  
};  
  
/// Typical HTTP request  
template<class Body = none, class Fields = fields>  
using request = message<true, Body, Fields>;  
  
/// Typical HTTP response  
template<class Body = none, class Fields = fields>  
using response = message<false, Body, Fields>;  
  
/// Serialize HTTP/1 message to a SyncWriteStream  
template<class SyncWriteStream, class DynamicBuffer,  
    bool isRequest, class Body, class Fields>  
void  
write(SyncWriteStream& stream, DynamicBuffer& dynabuf,  
    message<isRequest, Body, Fields> const& m);  
  
} // http  
} // beast  
```  
  
This is a fairly big API change and I'm interested in hearing from any/all stakeholders with feedback.  
  
---  
  
* rfc7230 section 3.2 refers to "Header Fields". **Fields** seams a reasonable synonym

---

## Comment 1

> Username: viccpp  
> Created at: 2016-11-08 06:54:03 UTC  
> Url: https://github.com/boostorg/beast/issues/171#issuecomment-259060338  

My 2 cents.  
  
What you intend to name "fields", RFC names "headers". So I suppose it will be confusing too.  
  
I name (in my library) request/response without body - "request_head" and "response_head". Not so pretty names but not ambiguous and short .

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-11-08 12:14:15 UTC  
> Url: https://github.com/boostorg/beast/issues/171#issuecomment-259121452  

Yeah these changes are not perfect, but I believe they are a step in the right direction. Currently we have `message_headers`, `headers`, `message_headers::headers`, and `message::headers`. Clearly, something needs to change :-)

---

## Comment 3

> Username: bachase  
> Created at: 2016-11-08 15:29:32 UTC  
> Url: https://github.com/boostorg/beast/issues/171#issuecomment-259167646  

IMHO, the only thing a bit awkward is that you have a single `headers` object that contains multiple `fields`.  Can a message have more than one `headers`? If not, perhaps call that struct `header` instead.

---

## Comment 4

> Username: wilsonianb  
> Created at: 2016-11-08 15:41:04 UTC  
> Updated at: 2016-11-08 15:41:33 UTC  
> Url: https://github.com/boostorg/beast/issues/171#issuecomment-259171027  

:+1: this fits well with the documented field concept  
https://vinniefalco.github.io/beast/beast/ref/Field.html  
I agree with @bachase that `header` (instead of `headers`) should be considered.

---

## Comment 5

> Username: viccpp  
> Created at: 2016-11-08 17:39:28 UTC  
> Url: https://github.com/boostorg/beast/issues/171#issuecomment-259205373  

> Clearly, something needs to change :-)  
  
Undoubtedly :-)
