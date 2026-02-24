# #1779 - Is the following a safe approach to determine if an HTTP response has a body or not? [Closed]

> Username: suryakalpo  
> Created at: 2019-12-13 20:19:35 UTC  
> Updated at: 2019-12-14 15:33:43 UTC  
> Closed at: 2019-12-14 15:33:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1779  

```  
http::response<http::dynamic_body> res;  
boost::beast::flat_buffer buffer;  
http::read(stream, buffer, res); // stream is an ssl::stream<tcp::socket> object  
  
auto body_size = get_optional_value_or(res.payload_size(), 0);  
if (body_size > 0 )  
{  
    response_body = boost::beast::buffers_to_string(res.body().data());  
}  
```  
  
The reason I ask this question is due to the following caveat in the documentation regarding payload_size()  
  
> If there is no body, this will return zero. Otherwise, if the body exists but is not known ahead of time, boost::none is returned (usually indicating that a chunked Transfer-Encoding will be used).  
  
If it is safe, then a follow-up question:  
Can http::response::body::data() be used as-is to read an HTTP body that is chunked?  
  
P.S: Apologies for the original post with the default Github template :)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-13 20:20:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1779#issuecomment-565594471  

Quoting the github issue template is definitely not going to help you determine if the HTTP response has a body :)

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-12-14 15:09:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1779#issuecomment-565725760  

It is safe.  
  
> Can http::response::body::data() be used as-is to read an HTTP body that is chunked?  
  
Yes. Whether or not the Transfer-Encoding is chunked has no effect on the resulting body, as Beast automatically removes the chunked encoding for you.

---

## Comment 3

> Username: suryakalpo  
> Created at: 2019-12-14 15:33:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1779#issuecomment-565727694  

Excellent! Thank you. I'll close this issue.
