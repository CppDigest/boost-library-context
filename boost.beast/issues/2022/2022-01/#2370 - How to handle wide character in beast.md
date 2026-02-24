# #2370 - How to handle wide character in beast [Closed]

> Username: JustKeepSilence  
> Created at: 2022-01-06 07:03:02 UTC  
> Updated at: 2022-01-06 12:17:47 UTC  
> Closed at: 2022-01-06 12:17:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2370  

### Version of Beast  
  
322  
  
when I send json string including wide charaters such as chinese characters from client(such as POSTMAN) using post method to my beast server, the contents of request.body() in beast is garbled .After checking my server code, I found the type of request in my code is [http::string_body](https://github.com/boostorg/beast/blob/develop/example/http/server/async/http_server_async.cpp#L258), I wonder if beast contains http::wstring_body?or how to handle wide character in beast server.My beast server code is from [examples](https://github.com/boostorg/beast/blob/develop/example/http/server/async/http_server_async.cpp)

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-01-06 08:05:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2370#issuecomment-1006359602  

You would need to use a multibyte conversion/rendering library.   
  
Beast has no knowledge of the specific encoding or semantic meaning of the data it transports. It simply transports the data and reproduces the headers that arrived in the request/response.  
  
Dealing with or multi-byte encoding is an application-level concern.  
  
There was a library proposed for boost to cover this: https://github.com/tzlaine/text but it was sadly rejected.  
  
You would start by interrogating the `Content-Encoding` header and interpreting the data appropriately from there.

---

## Comment 2

> Username: JustKeepSilence  
> Created at: 2022-01-06 12:17:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2370#issuecomment-1006537682  

@madmongo1 thanks, follow your suggestion, I tried [boost::locale::conv::to_utf<wchar_t>](https://www.boost.org/doc/libs/1_48_0/libs/locale/doc/html/charset_handling.html), convert requestBody to wString, parse it with [rapidjsonWDocument](http://rapidjson.org/md_doc_encoding.html) and solve the problem.
