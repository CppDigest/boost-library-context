# #884 - Implementation of sum/product Body types is impossible [Closed]

> Username: djarek  
> Created at: 2017-11-11 02:59:39 UTC  
> Updated at: 2017-12-16 03:44:07 UTC  
> Closed at: 2017-12-16 03:44:07 UTC  
> Url: https://github.com/boostorg/beast/issues/884  

It is impossible to compose Bodies into sum types, e.g.:  
```cpp  
template <typename... Ts>  
struct variant_body  
{  
    using value_type = std::variant<typename Ts::value_type...>;  
    struct reader;  
    struct writer;  
};  
  
using my_body = variant_body<beast::http::empty_body, json_body, beast::http::string_body>;  
```  
due to the requirement of Body::reader and Body::writer that the message's body type is exactly the same type as Body. The same restriction applies to product types(not as useful as a variant_body). Not sure how to make this use-case possible without complicating other use-cases.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-11 07:02:43 UTC  
> Url: https://github.com/boostorg/beast/issues/884#issuecomment-343645780  

How about if we change the constructor of reader and writer to something like this:  
```  
template<bool isRequest, class Fields>  
void writer(header<isRequest, Fields> const&, value_type const& body);  
```

---

## Comment 2

> Username: djarek  
> Created at: 2017-11-11 13:19:12 UTC  
> Url: https://github.com/boostorg/beast/issues/884#issuecomment-343664186  

I believe that would solve this issue.
