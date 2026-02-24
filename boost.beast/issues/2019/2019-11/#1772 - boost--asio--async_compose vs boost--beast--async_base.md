# #1772 - [question] boost::asio::async_compose vs boost::beast::[stable_]async_base [Closed]

> Username: AlexandreBossard  
> Created at: 2019-11-26 10:05:28 UTC  
> Updated at: 2020-01-03 20:44:19 UTC  
> Closed at: 2020-01-03 20:44:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1772  

Hi,  
  
I am writing composed operations around the beast API, I've read [the how to](https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/using_io/writing_composed_operations.html) already and successfully used the stable_async_base. Asio seems to have introduced [async_compose](https://www.boost.org/doc/libs/1_71_0/doc/html/boost_asio/reference/async_compose.html) recently (1.70 ?). I've rewrite my code with it, and aside from using a `unique_ptr` for memory 'stability' I haven't find much differences.  
  
My question is for the more seasoned C++ and asio devs around here, what are the pros and cons of both api ? Their compatibility with the (oncoming) Network TS ?

---

## Comment 1

> Username: cmazakas  
> Created at: 2019-11-26 15:41:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1772#issuecomment-558688122  

`async_compose` and `[stable_]async_base` have different purpposes.  
  
The async base classes are intended to give you simple ways of giving a Callable the executor/allocator hooks Asio requires. They also give you methods for maintaining the "deallocation before invocation" guarantee of Asio initiating functions as well.  
  
`async_compose` is a different and is a bit lower-level. `async_compose` is best suited when you already have an Asio initiating function and you wish to invoke a separation operation from within that one.  
  
A good example of this is in implementing servers. For example, I have a `listener` class that handles the creation and teardown of server-side connections but I use `async_compose` in the middle to dispatch to the user's request handler.  
  
You can see this here: https://github.com/LeonineKing1199/foxy/blob/develop/include/foxy/listener.hpp#L65-L90  
  
This works equally well for writing a generic client utility that also manages connection setup and teardown.  
  
So in short, `[stable_]async_base` is good for writing new, independent initiating functions while `async_compose` is good for dispatching to intermediate ones, whether they be user-customized or not.

---

## Comment 2

> Username: AlexandreBossard  
> Created at: 2019-11-26 22:35:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1772#issuecomment-558846990  

So, since I am writing new initiating functions which are mostly made of boost asio and beast ones, I could keep using `async_compose` ? The more I read about those (and the foxy codebase) the less I see a difference...  
  
Here is something simple I've wrote. It just calls `async_connect` then `async_handshake` on a `beast::ssl_stream`.  
  
```cpp  
namespace my_http  
{  
namespace detail  
{  
template <typename AsyncStream>  
struct connect_composer  
{  
  AsyncStream& stream;  
  tcp::resolver::results_type& results;  
  enum  
  {  
    starting,  
    connecting,  
    handshaking,  
  } _status = starting;  
  
  template <typename Self>  
  void operator()(Self& self, error_code ec = error_code{})  
  {  
    if (!ec)  
    {  
      switch (_status)  
      {  
      case starting:  
        _status = connecting;  
        beast::get_lowest_layer(this->stream)  
            .async_connect(this->results, std::move(self));  
        return;  
      case handshaking:  
        break;  
      }  
    }  
    self.complete(ec);  
  }  
  
  template <typename Self>  
  void operator()(Self& self,  
                  error_code ec,  
                  tcp::resolver::results_type::endpoint_type endpoint)  
  {  
    if (ec)  
    {  
      self.complete(ec);  
      return;  
    }  
    _status = handshaking;  
    this->stream.async_handshake(net::ssl::stream_base::client,  
                                 std::move(self));  
  }  
};  
}  
  
template <typename CompletionToken, typename AsyncStream>  
auto async_connect(AsyncStream& stream,  
                   tcp::resolver::results_type& results,  
                   CompletionToken&& token) ->  
    typename net::async_result<typename std::decay_t<CompletionToken>,  
                               void(error_code)>::return_type  
{  
  return net::async_compose<CompletionToken, void(error_code)>(  
      detail::connect_composer<AsyncStream>{stream, results}, token, stream);  
}  
}  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-11-26 22:57:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1772#issuecomment-558853099  

you should use `boost::asio::coroutine` instead of that `_status`

---

## Comment 4

> Username: AlexandreBossard  
> Created at: 2019-11-26 23:09:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1772#issuecomment-558856585  

I know, I will get to that, I am trying to wrap my mind around one boost lib/quirk at a time :)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-11-27 01:31:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1772#issuecomment-558888075  

It is mostly a matter of style and preference. Use whatever is easier for you to understand. I might use `async_compose` in Beast, for the composed operations that do not need stable allocations.

---

## Comment 6

> Username: AlexandreBossard  
> Created at: 2019-11-27 15:14:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1772#issuecomment-559128956  

`async_compose` seems to be easier to use if you don't need state/stable memory. Otherwise `*async_base` seems to be more compliant to the boost spec regarding allocators.  
  
Anyway thanks for your answers.

---

## Comment 7

> Username: stale[bot]  
> Created at: 2019-12-27 15:55:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1772#issuecomment-569295952  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: stale[bot]  
> Created at: 2020-01-03 20:44:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1772#issuecomment-570692803  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
