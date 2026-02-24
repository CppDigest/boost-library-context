# #1953 - How to retrieve POST query parameters? [Closed]

> Username: MarcoRhayden  
> Created at: 2020-05-13 22:39:53 UTC  
> Updated at: 2020-06-05 13:09:06 UTC  
> Closed at: 2020-06-05 13:09:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1953  

Hey all, i have a server and client using beast/http  
have some way available to retrieve POST query parameters in server side or i need to implement it my self?  
  
**Client:**  
```  
req.set(beast::http::field::content_type, "application/x-www-form-urlencoded");  
req.body() = "login=someUser&password=somePass";  
req.prepare_payload();  
```  
**Server:**  
```  
// The question is here, the library provides some way to receive the parameters,  
// or I need to create a specific method for that, such as a split from some delimiter  
req.body().data()  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-05-13 22:42:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1953#issuecomment-628283754  

Processing form data is a higher level concern than boost covers.  
  
Boost covers the HTTP (hypertext _transport_ protocol) and the websocket protocol.  
  
The semantics of GET/POST etc are a higher level concern.  
  
As things stand you will need to parse the form data yourself.

---

## Comment 2

> Username: MarcoRhayden  
> Created at: 2020-05-13 22:48:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1953#issuecomment-628285463  

I understand, that's what I've been doing, thanks for the information.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-05-13 23:10:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1953#issuecomment-628292268  

Keep an eye out for this later in the year: https://github.com/vinniefalco/url

---

## Comment 4

> Username: benstadin  
> Created at: 2020-06-02 21:50:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1953#issuecomment-637825367  

In the meantime you might have a look at the URI helper from the CPPRestSDK. Stripping it out as standalone helper is easy, I‘m using it with Beast for about a year without problems.  
  
[1] https://github.com/microsoft/cpprestsdk/blob/master/Release/src/uri/uri.cpp

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-06-02 22:07:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1953#issuecomment-637831408  

Or this https://github.com/cpp-netlib/uri

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-06-05 13:09:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1953#issuecomment-639469665  

Closing this issue as answered. If you have any more questions, please don't hesitate to ask.  
We live in the #beast channel of cpplang slack (but please read the manual before asking questions there).
