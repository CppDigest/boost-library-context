# #121 - HTTP 1 versus 2 namespacing [Closed]

> Username: ahmedcharles  
> Created at: 2016-10-10 13:09:26 UTC  
> Updated at: 2017-06-21 01:00:49 UTC  
> Closed at: 2017-06-21 01:00:49 UTC  
> Url: https://github.com/boostorg/beast/issues/121  

I decided to investigate how easy it would be to take the current method of suffixing `_v1` to names and include things using `_v2`, with ideally no collisions while each produces the same functionality. The process was basically:  
1. copy all files with `_v1` in the name to an equivalent `_v2` file.  
2. rename all of the things in the `_v2` files from `_v1` to `_v2`.  
3. fix up includes.  
4. compile and hope it works.  
  
Unfortunately, there are many declarations in the `_v1` files which basically pollute the namespace with unversioned names. So, I tried adding suffixes to the names that conflicted, either `_v1` or `_v2`, as appropriate. This took a few hours but eventually worked.  
  
However, I don't think this approach is reasonable and I'd prefer if the approach taken by `asio` with regard to ipv4 and ipv6, namely, namespaces. I think this situation would be much better with a `beast::http::v1::*` and `beast::http::v2::*`. Especially since implementing HTTP/2 will require tracking a 'session' per connection, because connections have state and multiplex streams, unlike HTTP/1 where Beast currently assumes that serializing a message to a stream is sufficient.  
  
I have some preliminary ideas for how HTTP/2 could be represented in Beast, but I'd rather focus on ensuring that the current interface doesn't make implementing HTTP/2 inelegant due to weird naming issues (for lack of a better way of putting it).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-10 13:31:34 UTC  
> Updated at: 2016-10-10 14:02:06 UTC  
> Url: https://github.com/boostorg/beast/issues/121#issuecomment-252619018  

Thank you for your interest in Beast! I think its great that you're looking into this!  
  
However, while the free function approach to parsing HTTP/1 messages is certainly elegant and simple, HTTP/2 is a different matter entirely. First I want to point out that I have designed Beast from the ground up with HTTP/2 in mind, so the design choices in Beast take it into account (there are also some other bigger changes coming to the message model and free function, to permit certain behaviors required by RFC7230 and RFC7231).  
  
The thing about HTTP/2 is that it is stateful. Its not possible to decode HTTP/2 messages without having a context in which to store metadata. A couple of examples:  
- Header compression has to keep a sliding window and dictionary state  
- Multiple messages can be incrementally received, requiring intermediate state to hold the data as it is received  
  
So we aren't going to be able to use the simple `read` and `write` interfaces that we have for HTTP/1.  
  
I'm leaning towards providing a "HTTP/2 session" object, modeled after `boost::asio::ssl::stream` and `beast::websocket::stream`. Something like this:  
  
```  
template<class NextLayer>  
class http::session_v2  
{  
  NextLayer next_layer_;  
  ...  
public:  
  ...  
  /** Deserialize a HTTP message from the stream.  
  
    @param id The message-identifier, unique for each message.  
    @param msg The received message.  
    @param ec Set to the error, if any occurred/  
  */  
  template<bool isRequest, class Body, class Headers>  
  void  
  read(  
    std::size_t& id,  
    message<isRequest, Body, Headers>& message,  
    error_code& ec);  
};  
```  
  
I think this would work pretty well. The scope of HTTP/2 is much bigger than HTTP/1, covering things like flow control and multiplexing. An object-based approach instead of free functions gives us much more flexibility in interface choice. And its a proven model (see `ssl::stream` and `websocket::stream`).  
  
I'd love to hear your thoughts on this design

---

## Comment 2

> Username: ahmedcharles  
> Created at: 2016-10-10 14:00:57 UTC  
> Url: https://github.com/boostorg/beast/issues/121#issuecomment-252629582  

Oh, that's exactly what I was thinking. I suppose the specific point I'm attempting to raise here is that Beast as it stands, effectively has namespace pollution, which wouldn't exist if suffixing were used more often or if namespaces were used for v1 vs v2.  
  
HTTP/2 being implemented as a stream is the obvious choice and in fact, you probably want it to be a session of streams, since stream is actually a term used to name a specific concept in HTTP/2. It'd be better to discuss this in another thread, though.  
  
However, the specific goal of this issue is about namespaces/naming, not really, HTTP/2 design.  
  
A good example of namespace pollution is that `parser_v1.hpp` declares the following in the `beast::http` namespace:  
- `detail::parser_request`  
- `detail::parser_response`  
- `skip_body`  
- `parser_v1`  
  
Only `parser_v1` is versioned and the others would likely have analogs in future parser implementations for v2 (and beyond, I suppose). Even the names in the detail namespace matter, since even though they are supposed to be private, they will conflict with a similar detail namespace used for implementing a parser for v2.  
  
However, if namespaces were used, this wouldn't be an issue, since you would have:  
- `v1::detail::parser_request`  
- `v1::detail::parser_response`  
- `v1::skip_body`  
- `v1::parser`  
  
And therefore, there would be no concern of collisions.  
  
Does the example help my point make more sense?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-10-10 14:11:08 UTC  
> Url: https://github.com/boostorg/beast/issues/121#issuecomment-252632193  

Yes, I see what you mean now. As I am still making some design changes (see https://github.com/vinniefalco/Beast/commits/message), there are likely to be naming problems such as the ones you pointed out. My plan is to iron these things out during the Boost formal review, but your input is valuable here.  
  
> you probably want it to be a session of streams, since stream is actually a term used to name a specific concept in HTTP/2.  
  
That's true. I've edited my prototype code to say `http::session_v2` instead.  
  
> Even the names in the detail namespace matter  
  
I'm not so sure about that. Since detail identifier names are not public, they are free to be renamed. When I add HTTP/2 support I can rename detail identifiers however I please to resolve conflicts.  
  
> ...since you would have:  
  
You listed the HTTP/1 parser and its accompanying options. So we're clear, you are proposing putting all HTTP/1 parser related routines in `namespace v1`? What about `beast::http::read`? And what about `beast::http::parse` (which is not specific to HTTP at all, works with any **Parser** concept)?  
  
What do we do later if we want to version the API, not for HTTP but for non backward compatible improvements? We would have burned the `v1` and `v2` namespaces.

---

## Comment 4

> Username: ahmedcharles  
> Created at: 2016-10-10 14:17:26 UTC  
> Url: https://github.com/boostorg/beast/issues/121#issuecomment-252634264  

I'm only proposing that the names in `*_v1.hpp` files be placed in a `beast::http::v1` namespace.  
  
This means that if the name is generic enough, it could be placed in another file. Like, `skip_body` can probably be placed in a file that's shared between v1 and v2, since in both cases, you may want to skip the body. But for example, the parser would be `v1::parser` and `v2::parser`.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-10-10 14:22:21 UTC  
> Updated at: 2016-10-10 14:22:33 UTC  
> Url: https://github.com/boostorg/beast/issues/121#issuecomment-252635463  

Okay, that's something to consider. Note that `message_v1.hpp` (and the `message_v1` class) will disappear in b16 or b17. So really, we're talking about the parser.

---

## Comment 6

> Username: ahmedcharles  
> Created at: 2016-10-10 14:34:56 UTC  
> Url: https://github.com/boostorg/beast/issues/121#issuecomment-252639548  

Ah, I just looked at the branch that you linked. It seems you moved the version to the headers object? Is there a reason for that, as opposed to the message object?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2016-10-10 14:40:35 UTC  
> Url: https://github.com/boostorg/beast/issues/121#issuecomment-252641159  

> ...you moved the version to the headers object? Is there a reason for that  
  
Yes. To support "Expect: 100-continue", Beast must provide an interface that allows you to send and receive a message without the body. And then conditionally finish sending or receiving the message body. `response_headers` and `request_headers` represent that part of the message, without the body.  
  
There is also another use-case, when building a server it might be desired to make a choice of the **Body** template type based on the contents of the headers. Having these additional classes makes that implementation possible.

---

## Comment 8

> Username: ahmedcharles  
> Created at: 2016-10-10 14:44:56 UTC  
> Url: https://github.com/boostorg/beast/issues/121#issuecomment-252642296  

Yeah, I just realized that `request_fields` was renamed to `request_headers`. I commented that I like the name `request_fields` slightly better, but now that I think more about it, I'm not so sure. In the use case you describe, it is quite literally, the `request_headers`, so it's not inaccurate at all.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2016-10-10 14:48:35 UTC  
> Url: https://github.com/boostorg/beast/issues/121#issuecomment-252643250  

There's going to be some identifier name volatility as we build out these changes so I'm not terribly worried about it at the moment - suggestions for alternatives are of course always welcomed (even if the conclusion is to not change something). And I'm certain that there will be a fair amount of bike shedding during the formal review :-)

---

## Comment 10

> Username: ahmedcharles  
> Created at: 2016-10-10 14:57:31 UTC  
> Url: https://github.com/boostorg/beast/issues/121#issuecomment-252646868  

`request_headers` is probably a better name. After all, my confusion only lasted 5-10 minutes, which probably means it's a great name. Significantly better than `x`. :)

---

## Comment 11

> Username: vinniefalco  
> Created at: 2016-10-10 15:04:04 UTC  
> Url: https://github.com/boostorg/beast/issues/121#issuecomment-252649957  

See #123

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-06-21 01:00:49 UTC  
> Url: https://github.com/boostorg/beast/issues/121#issuecomment-309932828  

This is all done, the library has undergone extensive renaming and tuning of the interfaces. I think its in a really good place now.
