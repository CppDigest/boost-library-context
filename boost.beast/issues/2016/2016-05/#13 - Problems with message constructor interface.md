# #13 - Problems with message constructor interface [Closed]

> Username: vinniefalco  
> Created at: 2016-05-18 11:30:27 UTC  
> Updated at: 2016-10-15 22:42:51 UTC  
> Closed at: 2016-10-15 22:42:51 UTC  
> Url: https://github.com/boostorg/beast/issues/13  

From @ja11sop  
  
First off, I really, really like Beast from what I've seen so far. Its integration with asio and the careful thought put into the design is evident unlike most other libraries in this space. As a result I've been tracking master in some local work and I've had to make a few changes based on construction of http requests - which is fine.  
  
However the change in this commit as referenced by the comment, "Previous constructors are removed as they were a notational convenience for assembling HTTP/1 requests and responses. They are not necessary as this library aims at library writers and not end users." leaves me a little perplexed.  
  
This is not about convenience - this is about providing a way to assemble an object so that construction, as far as possible, equals useful state. You have an object and it is in a useful state, or you don't. In general piecemeal construction is a bad idea and error-prone. If anything there should be a way to create a fully formed message at construction time alone. Modifiers should really only be there to handle the case where there is a time disparity with availability of information, in other words the information cannot be known at construction time or it is prohibitively expensive to make it so.  
  
I'd be interested in why piecemeal, property-based construction is seen as attractive? I don't see an advantage here, just opportunity for error and more code to write. I may of course be missing something.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-05-18 11:30:57 UTC  
> Url: https://github.com/boostorg/beast/issues/13#issuecomment-219999389  

Thank you for your interest in Beast, and thank you for the kind words!  
  
"...this library aims at library writers and not end users"  
  
An early critique of Beast was that in the example, it takes many lines of code just to send a HTTP request. Other libraries (libcurl for example) accomplish more in fewer lines, and even perform the resolution of domain names for you. Beast is not aimed at these use cases, it is instead aimed at the writers of such libraries.  
  
That said, I think what you're pointing out is the loss of this specific functionality:  
- For http requests, setting the method, uri, and version  
- For http responses, setting the status, obsolete-reason, and version  
  
The problem with the old constructors with the `request_params` and `response_params` is that we needed to reserve constructor arguments for initializing the header and body objects. In particular to support header and body types that are not default constructible. Or for header and/or body types that take multiple arguments to construct. So the `message` class was refactored to work like `std::pair`. As a side note, the `std::piecewise_construct` mechanism was invented precisely to solve the problem of constructing pair members that require allocators. I encountered an identical problem with `message`.  
  
I'm open to bringing the functionality of `request_params` and `response_params` back since you feel they are important (and I agree, having a way to construct an object in a fully valid state signals good design) but I'm not sure about how the signatures would look, due to some issues. There's no precedent for a tuple piecewise construction (3 or more elements each with their own parameter pack provided as tuple).  
  
Here's a possible signature for a new constructor that lets you initialize everything at once:  
  
```  
    template<class Params, class... Un, class... Vn>  
    message(std::piecewise_construct_t,  
            typename std::conditional<isRequest,  
                request_params, response_params>::type&& params,  
            std::tuple<Un...>&& un,  
            std::tuple<Vn...>&& vn);  
```  
  
But what if you don't want to provide the request or response specific fields at construction time? The addition of this constructor could create conflicts with the other constructors. I'm open to suggestions for new signatures.  
  
Thinking about this proposed signature, there's an extra complication. The `version` field was moved to the `message_v1` subclass. HTTP/2 does not have an individual version per message, there is instead a set of connection specific settings which can include features and a connection-wide version. `message` is designed to support HTTP/2, hence the field was moved.  
  
The `request_params` and `response_params` would not include the version. To make this work, we would have to add two new structures and new constructors for `message_v1`:  
  
```  
struct request_params_v1 : request_params  
{  
    int version;  
};  
  
struct response_params_v1 : response_params  
{  
    int version;  
};  
  
template<bool isRequest, class Body, class Headers>  
struct message_v1  
{  
    ...  
  
    template<class... Args>  
    message_v1(typename std::conditional<isRequest,  
        request_params_v1, response_params_v1>::type,  
            Args&&... args);  
};  
```  
  
This has problems too, I don't think an initializer-list can be used to initialize members in both the derived and the base class so the syntax `{"GET", "/", 11 }` would probably not work anymore. As you can see, adding support for initializing the request-specific and response-specific fields while also allowing piecewise construction with tuples for the header and the body, with the `message` base and `message_v1` derived classes, is decidedly non-trivial.  
  
And then we have the problem of `prepare`. Consider this statement (imagine it has additional constructors to permit initializing everything):  
  
```  
response_v1<string_body, headers> m(  
    { 200, "OK", 11},  
    { { "Server", "beast" }, { "Content-Type", "text/html" } },  
    "Here's the body");  
```  
  
How would the Content-Length, Connection, and Transfer-Encoding headers get set? They would have to be manually specified, we can't have the message constructor calling `http::prepare`.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-05-18 11:31:29 UTC  
> Url: https://github.com/boostorg/beast/issues/13#issuecomment-219999482  

From @ja11sop  
  
Hi Vinnie, hmm, yes I can see that after reading your response this is far from simple. I only have a few minutes to reply now so please don't read too much into this. In reading both responses the thought occurred to me that perhaps what is really need are some factory functions that return a valid/useful message. Since those factory functions would be part of the interface of the library and possibly a preferred method of construction it could help keep the constructors simpler. The disparity with `version` between v1 and v2 is a good example of why that might be best. (I was writing this reply when you posted your reference to `prepare` so not had time to consider that yet but I see it is problematic to say the least...)  
  
I know an argument could be made for saying such factory functions belong in a higher level library but I think the point is this library is the place where knowledge of what makes a valid object is contained. For example you might have something like this:  
  
``` cpp  
namespace http = beast::http;  
auto EmptyRequest = request( http::method("GET"), http::version(11), Url );  
```  
  
The factory function `request` could be found through ADL for example. There are issues with this sketch but I think they could be overcome. Internally the use of piecemeal construction is hidden from the user and only a valid object is returned.

---

## Comment 3

> Username: ja11sop  
> Created at: 2016-05-19 00:05:42 UTC  
> Url: https://github.com/boostorg/beast/issues/13#issuecomment-220193471  

Thanks for moving this discussion to a more public place. I'll circle back soon with more concrete thoughts on this.

---

## Comment 4

> Username: bitbugprime  
> Created at: 2016-05-25 16:35:16 UTC  
> Url: https://github.com/boostorg/beast/issues/13#issuecomment-221632703  

I just checked in and saw many things have changed. For example, methods in request objects are manually specified strings and not enums now? What is the _v1 stuff? What does prepare do? How about a before/after code example to explain these changes?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-05-25 16:41:17 UTC  
> Updated at: 2016-05-25 16:54:10 UTC  
> Url: https://github.com/boostorg/beast/issues/13#issuecomment-221634438  

@randian Copied your question to a new issue: #17

---

## Comment 6

> Username: ghost  
> Created at: 2016-09-24 19:44:55 UTC  
> Updated at: 2016-09-24 19:45:44 UTC  
> Url: https://github.com/boostorg/beast/issues/13#issuecomment-249383722  

*parth <-> part @e-fominov

---

## Comment 7

> Username: vinniefalco  
> Created at: 2016-09-25 00:32:53 UTC  
> Url: https://github.com/boostorg/beast/issues/13#issuecomment-249395393  

@e-fominov Can you please create a new issue and copy your questions into it? I will respond to the new issue - this will make it easier for other people to find the questions and answers, thanks!

---

## Comment 8

> Username: vinniefalco  
> Created at: 2016-10-14 22:47:41 UTC  
> Url: https://github.com/boostorg/beast/issues/13#issuecomment-253937856  

A lot has happened since this issue was opened, the focus of the library has more clarity, more people are using it, and the message interface has evolved. Thinking about constructing messages which have all fields upon construction, it does not seem to be an important use case. And we've already overloaded the constructor significantly to support the headers and body types. For this reason, I am going to consider this resolved. However, I will leave the issue open for interested readers, and in case anyone wants to present a case for why this should be reconsidered.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2016-10-15 22:42:51 UTC  
> Url: https://github.com/boostorg/beast/issues/13#issuecomment-254015491  

Closing this as Not A Defect
