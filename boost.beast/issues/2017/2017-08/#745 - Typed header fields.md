# #745 - [Proposal] Typed header fields [Closed]

> Username: DoumanAsh  
> Created at: 2017-08-18 11:01:22 UTC  
> Updated at: 2017-09-02 18:33:19 UTC  
> Closed at: 2017-08-24 20:01:30 UTC  
> Url: https://github.com/boostorg/beast/issues/745  

At the moment I'm [playing](https://github.com/DoumanAsh/gomibako.cpp/) around with Beast to see its capabilities and aside from weak docs (which is though typical to most boost libs) I have feeling that option for strongly typed header fields would make sense.  
  
Important note: I believe current option to set raw strings as header's fields should remain.  
  
## What is typed header field?  
  
Simply put it a way to let user set valid fields  
  
For example let's say we want to set ETag.  
As we're aware there are two types:  
* Strong - `"some_unique_id"`  
* Weak - `W/"some_unique_id"`  
  
So in order to properly set field user needs, depending whether he wants strong or weak, prepend or not `W/`  
It is a simple example and not really complex for user.  
Still instead of asking for string we could let user have API like that:  
  
```c++  
response->set(http::typed_field::ETag("unique_id", true)) //Where second argument is whether etag is strong or not  
```  
  
Now we can consider for example setting Date:  
```c++  
response->set(http::typed_field::Date(std::chrono::now())  
```  
Date would certainly helpful as user wouldn't need to worry about its formatting accordingly to [RFC](https://tools.ietf.org/html/rfc7231#section-7.1.1.2)  
It is certainly what I'd expect from http library: to deal with all http stuff for me.  
  
Now for example Cache Control  
```c++  
static http::typed_fields::CacheControl   
default_cache_control(http::typed_fields::CacheControl::NoCache,  
                                  http::typed_fields::CacheControl::Private.  
                                  http::typed_fields::CacheControl::Extension(name, std::optional<argument>)  
)  
  
response->set(default_cache_control)  
```  
  
Again, user don't need to worry about how to set field.  
He can just use typed fields to get it properly set.  
In case there is need for extensibility we can provide special typed field that would allow setting custom cache directive.  
But again formatting of field's value will be left to beast, which is good.  
  
## Does low-level library needs such thing?  
While beast seems to be rather low level library, I believe it should handle all hassle of working with HTTP protocol.  
The only thing would be expected of user is to read documentation and know basics of protocol.  
  
Second thing: typed fields would make easier to avoid setting invalid headers as beast would handle all aspects of RFC. So as long as user uses typed fields, he can expect that headers are correct in his messages.  
  
## Final words  
This is of course a complex idea and I don't expect to see it soon.  
I wouldn't mind helping with it but as I'm not yet experienced with Beast I for now decided to just leave this idea for consideration.  
  
P.s. The idea is actually came to me from [hyper](https://github.com/hyperium/hyper) http library for Rust

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-18 15:27:00 UTC  
> Updated at: 2017-08-18 15:27:14 UTC  
> Url: https://github.com/boostorg/beast/issues/745#issuecomment-323384583  

>Beast...aside from weak docs (which is though typical to most boost libs)   
  
Is there something missing from the Beast documentation? What information were you trying to find?  
  
>While beast seems to be rather low level library, I believe it should handle all hassle of working with HTTP protocol.  
  
Hmm, no that is not the intended design. Beast is a low level HTTP protocol layering on top of Asio. It does not try to handle all aspects of HTTP. That is the responsibility of higher level libraries.  
  
I think the features that you're asking for are best implemented in another library. It could be easily done using a set of free function overloads instead of modifying `beast::http::basic_fields`. For example:  
  
```  
template<class Allocator>  
void  
set(basic_fields<Allocator>& fields,  
    http::typed_fields::CacheControl const& value);  
```  
  
>I wouldn't mind helping with it  
  
I encourage you to write this as a separate library!  
  
There is one small problem though. This features addresses the setting of fields, which in my opinion is the easier part. Much harder is to handle the parsing of the fields. Such as the Date field.

---

## Comment 2

> Username: DoumanAsh  
> Created at: 2017-08-18 16:02:19 UTC  
> Updated at: 2017-08-18 16:05:40 UTC  
> Url: https://github.com/boostorg/beast/issues/745#issuecomment-323393650  

>Is there something missing from the Beast documentation? What information were you trying to find?   
  
Overall it is difficult to determine what exactly is pattern in using beast.  
While on surface you have the same asio, there is more depth due to complexity of HTTP protocol.  
  
For example I still haven't understood whether I'm better to use request/response parsers or not.  
It seems I can pass request directly to `async_read` and get it parsed as it is.  
I didn't try, but i guess I would be also able to read only headers into it?  
  
There is another fundamental issue, but it is issue of asio: There is not exactly clear picture how your async code should look.  
I had initial struggle experimenting with my HTTP server to determine whether my handlers are safe to use in multi threaded environment or not.  
  
Lack of in-depth tutorial which would cover from the beginning to end writing a proper HTTP server would be really nice. Focus of course would on architecture of beast as library.  
What types you should use and how should you use library:  
1. In-depth on handling requests and writing responses:  
    * Possible ways and their pros/cons   
2. Examples could be more commented.  
    * Often I could find comments to stay obvious things like "It closes socket" and etc.  
    * There are though some details I appreciate when reading examples: for example nice way to use `std::optional` to reset request/response  
3. To be honest Boost format makes it difficult to browse through Reference API when I need to look-up different classes/structs. It is a bit painful since beast has lots of own types.  
4. Many topics in Chapter "Using HTTP" feels disconnected. You may have idea about topics in these chapters, but overall picture is not complete.  
  
  
Eventually I dropped reading documentation in favor of looking at examples :)  
And right now i'm thinking of how to make myself a proper routing mechanism before i'll continue to dive into beast.  
  
>Hmm, no that is not the intended design. Beast is a low level HTTP protocol layering on top of Asio. It does not try to handle all aspects of HTTP. That is the responsibility of higher level libraries.  
  
I see, in that case I'd think of making it as a separate library.  
I suppose I then close this issue, but if I would like to discuss the topic of such library where could i reach you?  
  
>There is one small problem though. This features addresses the setting of fields, which in my opinion is the easier part. Much harder is to handle the parsing of the fields. Such as the Date field.  
  
Yes, I thought to start with easiest part as defining typed headers and setting them.  
Parsing into typed headers not only complex, but there is a problem of how to treat errors.  
But eventually I'll think about it after I'll finish with routing in my simple server.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-08-18 16:24:30 UTC  
> Updated at: 2017-08-18 16:25:17 UTC  
> Url: https://github.com/boostorg/beast/issues/745#issuecomment-323398789  

>I didn't try, but i guess I would be also able to read only headers into it?  
  
Yes. If you want to read just headers, then use `read_header` or `async_read_header`  
  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__read_header/overload2.html  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html  
  
Have you seen this page?  
  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_http.html  
  
>For example I still haven't understood whether I'm better to use request/response parsers or not. It seems I can pass request directly to async_read and get it parsed as it is.   
  
You use a `parser` when you need the features. For example the parser lets you adjust the limit on the size of the body. And if you want to do incremental reading, such as reading only 2KB at a time, you also need a parser. The functions `read_some` and `async_read_some` are provided for that purpose:  
  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__read_some/overload2.html  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_some.html  
  
>There is another fundamental issue, but it is issue of asio: There is not exactly clear picture how your async code should look. I had initial struggle experimenting with my HTTP server to determine whether my handlers are safe to use in multi threaded environment or not.  
  
Yes, I know what you mean. Beast assumes that you already know Asio. It is beyond the scope of the beast documentation to also teach the user Asio. Despite this, there is some material which covers advanced Asio topics, such as writing your own composed operations.  
  
There are already many tutorials on writing asynchronous servers with Asio, including the Asio examples, I think those are the best place to learn.  
  
>Lack of in-depth tutorial which would cover from the beginning to end writing a proper HTTP server would be really nice.  
  
That is beyond the scope of the documentation, because much of that is covered by Asio best practices. Beast does come with many example servers which you can use to get started.  
  
>right now i'm thinking of how to make myself a proper routing mechanism before i'll continue to dive into beast.  
  
This might interest you:  
  
https://cppcon2017.sched.com/event/Bgsh/using-functional-programming-patterns-to-build-a-clean-and-simple-http-routing-api  
  
> if I would like to discuss the topic of such library where could i reach you?  
  
We can discuss it in the issue, I'll re-open it  
  
Thank you for your feedback!

---

## Comment 4

> Username: DoumanAsh  
> Created at: 2017-08-18 16:42:18 UTC  
> Updated at: 2017-08-18 16:42:27 UTC  
> Url: https://github.com/boostorg/beast/issues/745#issuecomment-323402996  

>You use a parser when you need the features. For example the parser lets you adjust the limit on the size of the body  
  
Wouldn't it be the same possible using just flat_buffer as body?  
  
>Yes, I know what you mean. Beast assumes that you already know Asio. It is beyond the scope of the beast documentation to also teach the user Asio. Despite this, there is some material which covers advanced Asio topics, such as writing your own composed operations.  
  
While we're on the topic, would you be so kind to point out proper materials on advanced usage of boost::asio  
Google gives me rather scattered materials and asio's tutorial itself seems to be rather basic.  
  
>There are already many tutorials on writing asynchronous servers with Asio, including the Asio examples, I think those are the best place to learn.  
  
Yep, I started with simple async TCP server first to understand asio but it felt rather basic to me.  
But well by now I have some grasp on both asio and beast after making working tcp and http servers.  
  
>This might interest you:  
https://cppcon2017.sched.com/event/Bgsh/using-functional-programming-patterns-to-build-a-clean-and-simple-http-routing-api  
  
Indeed, sadly I'm not from USA and I don't think I'll be able to prepare myself a trip to CppCon.  
But hopefully there will be recording of this talk.  
  
>We can discuss it in the issue, I'll re-open it  
  
Ah, I thought it would not appropriate as this a topic of high level library.  
But anyway thanks, I'll keep you posted

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-08-18 16:48:39 UTC  
> Updated at: 2017-08-18 16:49:15 UTC  
> Url: https://github.com/boostorg/beast/issues/745#issuecomment-323404529  

>Wouldn't it be the same possible using just flat_buffer as body?  
  
It isn't the same. The parser detects that the Content-Length exceeds the limit as soon as it reads the header. Otherwise, you are stuck reading the body until the `flat_buffer` throws. There are other reasons to use a parser though. You need to read incrementally if you want to set reasonable timeouts.  
  
>would you be so kind to point out proper materials on advanced usage of boost::asio  
  
I found this on the first try:  
  
https://www.gamedev.net/blogs/entry/2249317-a-guide-to-getting-started-with-boostasio/

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-08-24 20:01:30 UTC  
> Url: https://github.com/boostorg/beast/issues/745#issuecomment-324741390  

As this is out of scope, I'm going to go ahead and close this. But if you are interested in implementing it yourself I would be more than happy to answer any questions you might have or offer advice if you need it, just open a new issue or comment on this one. Thanks!

---

## Comment 7

> Username: DoumanAsh  
> Created at: 2017-08-31 18:09:19 UTC  
> Url: https://github.com/boostorg/beast/issues/745#issuecomment-326377590  

@vinniefalco I have small question about `boost::string_param`  
Is it supposed to be safely moved?  
I see that copy constructor is deleted for obvious reasons and so i decided to try first to do something like that  
```c++  
boost::beast::string_param&& ETag::value() const {  
    return std::move(boost::beast::string_param(this->weak ? "W/\"" : "\"", this->tag, "\""));  
}  
```  
  
But my test shows strange result:  
```c++  
boost::beast::string_param expected_value("W/\"", tag, "\"");  
std::cout << "value='" << etag.value().str() << "' | "  
              << "expected_value='" << expected_value.str() << "'\n";  
BOOST_REQUIRE_EQUAL(expected_value.str(), etag.value().str());  
```  
  
Output:  
```  
Running 1 test case...  
value='@ﾏOUﾅ   ・ | expected_value='W/"OLOLO"'  
D:/repos/cpp-code/gomibako/test/http/typed_headers.cpp(22): fatal error: in "should_correctly_create_weak_etag": critical check expected_value.str() == etag.value().str() has failed [W/"OLOLO" != xﾏOUﾅ   疹  
```  
  
Could it be that string_param would require manual implementation of move constructor?  
To be honest I'm not exactly sure what are default move constructor

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-08-31 18:42:55 UTC  
> Url: https://github.com/boostorg/beast/issues/745#issuecomment-326386270  

You should not be using `string_param` as a return value. As the name suggests, it is only for function parameters.

---

## Comment 9

> Username: DoumanAsh  
> Created at: 2017-09-02 15:21:12 UTC  
> Url: https://github.com/boostorg/beast/issues/745#issuecomment-326750495  

@vinniefalco A bit unrelated topic but I noticed that there are compression streams in API Reference, but I haven't found concrete examples or information in tutorial on this topic.  
  
Could it be I'm missing something?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-09-02 18:33:19 UTC  
> Url: https://github.com/boostorg/beast/issues/745#issuecomment-326762121  

The zlib interfaces are experimental
