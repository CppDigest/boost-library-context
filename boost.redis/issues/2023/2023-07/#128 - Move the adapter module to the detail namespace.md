# #128 - Move the adapter module to the detail namespace [Closed]

> Username: mzimbres  
> Created at: 2023-07-29 18:47:30 UTC  
> Updated at: 2024-12-22 10:43:40 UTC  
> Closed at: 2024-12-22 10:43:40 UTC  
> Url: https://github.com/boostorg/redis/issues/128  

No elements of the adapter module appears in the public API so it should be moved in the detail namespace and directories. Some parts of it belong into `redis/detail` while other in `redis/resp3/detail`.

---

## Comment 1

> Username: GoldRobot  
> Created at: 2024-05-08 18:01:23 UTC  
> Url: https://github.com/boostorg/redis/issues/128#issuecomment-2101118740  

Hello. Not sure what exactly planned to be moved, so let me ask a question.  
  
I found 'boost::redis::adapter::boost_redis_adapt' function in redis/adapter/adapter.hpp, and used it to type erasue boost::redis::response, while still taking advantage of it's statis typing.  
I use that to pimpl-wrap connection, to limit headers leaking to only what needed for boost::redis::request/response/adapter.  
  
I actually thought that was kinda planned, because 'response' argument for async_exec is fully templated and require only boost_redis_adapt function to be defined.  
  
My naive approach:  
```cpp  
class ResponseHider  
{  
    using Call = std::function<void(std::any &, std::size_t i, boost::redis::resp3::basic_node<std::string_view> const &nd, boost::system::error_code &ec)>;  
    using GetSupportedResponseSize = std::function<size_t(const std::any &)>;  
      
    std::any adapter_;  
    Call call_;  
    GetSupportedResponseSize get_supported_response_size_{};  
    
  public:  
    template<class ...Results>  
    explicit ResponseHider(boost::redis::response<Results...> &response)  
    {  
        using Adapter = std::decay_t<decltype(boost_redis_adapt(response))>;  
        adapter_ = boost_redis_adapt(response);  
        // No capture, any as argument with recasting in runtime to not bother with copy/move constructors  
        call_ = [](std::any &any_adapter, auto &&...args)  
        {  
            auto &adapter = std::any_cast<Adapter &>(any_adapter);  
            adapter(std::forward<decltype(args)>(args)...);  
        };  
        get_supported_response_size_ = [](const std::any &any_adapter)  
        {  
            const auto &adapter = std::any_cast<const Adapter &>(any_adapter);  
            return adapter.get_supported_response_size();  
        };  
    }  
      
    void operator()(std::size_t i, boost::redis::resp3::basic_node<std::string_view> const &nd, boost::system::error_code &ec)  
    {  
        return call_(adapter_, i, nd, ec);  
    }  
      
    [[nodiscard]]  
    auto get_supported_response_size() const noexcept  
    {  
        return get_supported_response_size_(adapter_);  
    }  
};  
  
ResponseHider boost_redis_adapt(ResponseHider &t) noexcept;  
```  
ResponseHider objects then can be passed into async_exec in implementation files.  
  
I thought that templated response (requiring just adapter for it) is exactly for it, to customize responses, giving also way to not reimplement deserialization of resp protocol by using original adapters.  
  
That kind of usage wasn't planned?

---

## Comment 2

> Username: mzimbres  
> Created at: 2024-05-26 18:34:42 UTC  
> Url: https://github.com/boostorg/redis/issues/128#issuecomment-2132329707  

Hi, sorry for being so late. Before the inclusion in Boost `connection::async_exec` used to take an adapter instead of a response parameter  
  
```cpp  
# Present  
conn.async_exec(request, response, token);  
  
# Before Boost  
conn.async_exec(request, adapt(response), token);  
```  
  
In the Boost review however somebody raised the question about _why I have to call adapt()?_ and I decided to hide its call [here](https://github.com/boostorg/redis/blob/a4d2bb983dba0aac546a41d04f6af94568e817fc/include/boost/redis/detail/connection_base.hpp#L447).  This resulted in a simpler API that is however harder to type-erase as you correctly noted (type-erasure occurrs [here](https://github.com/boostorg/redis/blob/a4d2bb983dba0aac546a41d04f6af94568e817fc/include/boost/redis/detail/connection_base.hpp#L623) btw).  
  
There is however a simple fix for that: Move the adapter out of the inner layer to expose it to the user as originaly thought. That would result in [another](https://github.com/boostorg/redis/blob/a4d2bb983dba0aac546a41d04f6af94568e817fc/include/boost/redis/connection.hpp#L395) `async_exec` overload, namely  
  
```cpp  
# Current overload  
template <class Response, class CompletionToken>  
auto async_exec(request const& req, Response& resp, CompletionToken&& token);  
  
# Overload that takes the currently internal type-erased adapter.  
template <class CompletionToken>  
auto async_exec(request const& req, adapter_type adapter, CompletionToken&& token);  
```  
  
By using `asio::any_completion_handler` the implementation of this overload can be completely moved to the .ipp file as I did for `async_run` and you wouldn't need any more work on your side.  
  
For family reasons however I don't have much time to dedicate to this feature in the upcomming weeks (second baby comes in a week) but if you are lucky some Asio expert such as @anarthal might get interest in implementing it. If you want to folow this path however, please open a new ticket with reference to this one, thanks.

---

## Comment 3

> Username: anarthal  
> Created at: 2024-05-27 09:49:28 UTC  
> Url: https://github.com/boostorg/redis/issues/128#issuecomment-2133101647  

If we're sure removing `adapt` is not considered a breaking change (because it was never part of the public API in the first place) we should do that ASAP before people end up using it.  
  
> By using asio::any_completion_handler the implementation of this overload can be completely moved to the .ipp file as I did for async_run and you wouldn't need any more work on your side.  
  
This is true. @GoldRobot is still going to have to pimpl away the connection, as including `<asio/ssl/...>` takes the order of seconds on itself. He may be able to do the same without the use of `any_completion_handler`, as long as we provide a way to type-erase the adapter type. @GoldRobot , which completion style are you using? (callbacks, `yield_context`, C++20 coroutines...)  
  
@GoldRobot I found myself in a similar need with Redis, and came up with [this scheme](https://github.com/anarthal/servertech-chat/blob/master/server/include/services/redis_client.hpp). Essentially, you build a service class (using pimpl or virtuals) where you expose a repository-like API. The style is a little old-fashioned, but gets the job done, achieves a pretty good level of separation of concerns, and is very well suited to run unit tests using mocks.  
  
Another option (not ideal, but also gets the job done) is to use explicit template instantiations:  
  
```  
// your pimpl connection.hpp  
class pimpled_connection {  
    unique_ptr<impl_t> impl_;  
public:  
    template <class Response>  
    asio::awaitable exec(const redis::request& req, Response& res); // not defined here  
};  
  
// in a .cpp file that uses pimpl connection  
asio::awaitable handle_request(pimpled_connection& conn) {  
    redis::request req; // compose the request  
    std::vector<std::string> res; // response to use   
    co_await conn.exec(req, res);  
}  
  
// in pimpl connection.cpp,  
// define your template  
template <class Response>  
asio::awaitable pimpled_connection::exec(const redis::request& req, Response& res) { /* ... */ }  
  
// explicitly instantiate your template  
template asio::awaitable pimpled_connection::exec<std::vector<std::string>>(const redis::request&, std::vector<std::string>);  
```  
  
Even if a generic type-eraser is the ideal, I'd avoid it for now until we understand whether `boost_redis_adapt` is going to be hidden in the next release - otherwise your code will stop working when you upgrade Boost.  
  
Moving forward with the Redis issue. What you're calling `adapter_type` is probably better named `any_response_ref`, as from the user's point of view, you're just doing type-erasing.  
  
I need to understand a couple of things before doing anything:  
a. Reading adapter code (like [this](https://github.com/boostorg/redis/blob/develop/include/boost/redis/adapter/detail/adapters.hpp#L187-L188)), it looks like adapters have data members keep intermediate parsing state. This suggests me that you've got support for stream parsing, where the message is received in chunks, so the parser can't rely on having the entire message from the beginning.  
b. However, looking at [how it is used](https://github.com/boostorg/redis/blob/a4d2bb983dba0aac546a41d04f6af94568e817fc/include/boost/redis/detail/connection_base.hpp#L341), it looks like no stream parsing in happening.  
c. Is stream parsing really happening or not? If it's not, we can refactor parsing so that adapters don't keep any state, which makes them much easier to type erase (just a couple of pointers).  
  
If you're happy with this change, I can implement it and PR (might be big depending on what you answer).  
  
Also, if you're going to be unavailable to maintain Boost.Redis, I can probably dedicate some time to doing it moving forward.

---

## Comment 4

> Username: GoldRobot  
> Created at: 2024-05-27 16:12:07 UTC  
> Url: https://github.com/boostorg/redis/issues/128#issuecomment-2133783868  

> For family reasons however I don't have much time to dedicate to this feature in the upcomming weeks (second baby comes in a week)  
  
congratulations and all the best man :)  
  
> @GoldRobot , which completion style are you using? (callbacks, `yield_context`, C++20 coroutines...)  
  
Callbacks, unfortenatly.  
  
> @GoldRobot I found myself in a similar need with Redis, and came up with [this scheme](https://github.com/anarthal/servertech-chat/blob/master/server/include/services/redis_client.hpp).  
  
Kind of that what I done, difference is that my class expose templated response argument. Basically I wrap exactly connection, to then reference it from logical blocks. So additional layer between boost::redis::connection and chat::redis_client.  
Decided to make it like that, because I have more than one logical part (like chat::redis_client), so I pimpled only the heaviest part once, boost::redis::connection itself. Alternative is 'pimpling' each part, which is a bit tedious, or combine different logical parts into one.  
  
> Even if a generic type-eraser is the ideal, I'd avoid it for now until we understand whether `boost_redis_adapt` is going to be hidden in the next release - otherwise your code will stop working when you upgrade Boost.  
  
Understood.  
As user, override alternative looks great to me. However I want to note that current way let me type_erase with nearly zero wheel reinventing. What lives in boost::redis::adapters::detail is not just type_erasue tool, but statically typing deserialization building, which have real value on it's own. How I see it, one might actually use that part of library without using boost::redis::connections. But I not 'asio expert', nor expert in designing libraries interfaces at all, just my 2cents.  
  
-----  
  
Other questions is for [mzimbres](https://github.com/mzimbres), but in case he is busy, what I found.  
  
> b. However, looking at [how it is used](https://github.com/boostorg/redis/blob/a4d2bb983dba0aac546a41d04f6af94568e817fc/include/boost/redis/detail/connection_base.hpp#L341), it looks like no stream parsing in happening.  
  
As I see, resp3::parse return true only [if whole response was consumed](https://github.com/boostorg/redis/blob/a4d2bb983dba0aac546a41d04f6af94568e817fc/include/boost/redis/resp3/parser.hpp#L86) (or if error occurs).  
Untill then, [parser::consume](https://github.com/boostorg/redis/blob/a4d2bb983dba0aac546a41d04f6af94568e817fc/include/boost/redis/resp3/impl/parser.ipp#L73) get called, which return `std::optional<node_type>`. If consume return value, then adapter get called and parse-cycle repeats. If consume return nothing, then resp3::parse returns false.  
  
resp3::parse result used by [on_read](https://github.com/boostorg/redis/blob/a4d2bb983dba0aac546a41d04f6af94568e817fc/include/boost/redis/detail/connection_base.hpp#L910). If resp3::parse result returned false (parser::consume said need more basically), on_read convert it to parse_result::needs_more.  Which means append_some_op will happen in cycle inside of reader_op.  
  
resp3::parser track number of 'consumed' (readed actually) bytes, untill [reset](https://github.com/boostorg/redis/blob/a4d2bb983dba0aac546a41d04f6af94568e817fc/include/boost/redis/resp3/impl/parser.ipp#L28) get called. reset called inside of [on_finish_parsing](https://github.com/boostorg/redis/blob/a4d2bb983dba0aac546a41d04f6af94568e817fc/include/boost/redis/detail/connection_base.hpp#L862), along with buffer clearing.  
  
So stream-parsing is happens. It's minimal element is resp3::node, and buffer never get cleared untill whole response message readed. But based of presence of [std::size_t here](https://github.com/boostorg/redis/blob/a4d2bb983dba0aac546a41d04f6af94568e817fc/include/boost/redis/detail/connection_base.hpp#L860), I suspect there was plan to consume parsed bytes from buffer on go, but right now it's [always zero](https://github.com/boostorg/redis/blob/a4d2bb983dba0aac546a41d04f6af94568e817fc/include/boost/redis/detail/connection_base.hpp#L910).

---

## Comment 5

> Username: mzimbres  
> Created at: 2024-05-27 22:14:32 UTC  
> Url: https://github.com/boostorg/redis/issues/128#issuecomment-2134097557  

> as long as we provide a way to type-erase the adapter type.  
  
The internal connection type (`connection_base`) uses a  
  
```cpp  
std::function<void(node_type const&, system::error_code&)>;  
```  
  
to type-erase it.  
  
> Moving forward with the Redis issue. What you're calling adapter_type is probably better named any_response_ref, as from the user's point of view, you're just doing type-erasing.  
  
The adapter is lightweight handle to the response and knows how to fill it as chunks of RESP3 arrive on the socket.  
  
> I need to understand a couple of things before doing anything:  
  
> a. Reading adapter code (like this), it looks like adapters have  
> data members keep intermediate parsing state. This suggests me that  
> you've got support for stream parsing, where the message is received  
> in chunks, so the parser can't rely on having the entire message  
> from the beginning.  
  
Correct, Boost.Redis implements a sans-IO stream parser. It could be even moved into a separate library to complement Boost.Serialization and Boost.Json.  
  
> b. However, looking at how it is used, it looks like no stream  
> parsing in happening.  
  
The connection class will pass incomming data to the parser until it is done with the message and after that it is [reset](https://github.com/boostorg/redis/blob/a4d2bb983dba0aac546a41d04f6af94568e817fc/include/boost/redis/detail/connection_base.hpp#L875).  
  
> c. Is stream parsing really happening or not? If it's not, we can  
> refactor parsing so that adapters don't keep any state, which makes  
> them much easier to type erase (just a couple of pointers).  
  
Yes, we need it. There is no way of knowing the size of e.g. agreegate message types in advance.  
   
> If you're happy with this change, I can implement it and PR (might  
> be big depending on what you answer).  
  
Yes, that is fine. I expect something along the lines of my previous message, where another async_exec is provided, which takes an adapter as parameter.  
   
> Also, if you're going to be unavailable to maintain Boost.Redis, I  
> can probably dedicate some time to doing it moving forward.  
  
That would be nice, thanks. I am not going to say I will be completely unavailable, but only that I won't engage in larger features. Help is much appreciated.

---

## Comment 6

> Username: mzimbres  
> Created at: 2024-05-27 22:22:49 UTC  
> Url: https://github.com/boostorg/redis/issues/128#issuecomment-2134102857  

A simpler overview about how the parser works can be seem [here](https://github.com/boostorg/redis/blob/a4d2bb983dba0aac546a41d04f6af94568e817fc/include/boost/redis/resp3/serialization.hpp#L112) (which is only suitable for whole messages) and it usage is shown [here](https://github.com/boostorg/redis/blob/a4d2bb983dba0aac546a41d04f6af94568e817fc/test/test_low_level_sync_sans_io.cpp#L28).

---

## Comment 7

> Username: anarthal  
> Created at: 2024-05-28 08:58:01 UTC  
> Url: https://github.com/boostorg/redis/issues/128#issuecomment-2134692656  

Looks like I completely misread the code involving stream parsing, thanks @GoldRobot for your detailed analysis.  
  
Looks like we're all agreeing on exposing a type-erased response adapter as the best solution. I'll try to work that out and PR you.
