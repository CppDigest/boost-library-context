# #245 - RedisJson adapter issues with class with private fields [Closed]

> Username: eaziz4  
> Created at: 2025-04-28 17:14:08 UTC  
> Updated at: 2025-09-08 20:37:31 UTC  
> Closed at: 2025-09-08 20:37:31 UTC  
> Url: https://github.com/boostorg/redis/issues/245  

I have a class "Bet" that has many private fields, one of which is another class, "Event", with private fields.   
I have created the following functions that should be registered globally(I just copied the example documentation for these and changed the class type)  
```  
void boost_redis_to_bulk(std::string& to, Bet const& u)  
{  
    boost::redis::resp3::boost_redis_to_bulk(to, boost::json::serialize(boost::json::value_from(u)));  
}  
  
void boost_redis_from_bulk(  
   Bet& u,  
   boost::redis::resp3::node_view const& node,  
   boost::system::error_code&)  
{  
    u = boost::json::value_to<Bet>(boost::json::parse(node.value));  
}  
  
void boost_redis_to_bulk(std::string& to, Event const& u)  
{  
    boost::redis::resp3::boost_redis_to_bulk(to, boost::json::serialize(boost::json::value_from(u)));  
}  
  
void boost_redis_from_bulk(  
   Event& u,  
   boost::redis::resp3::node_view const& node,  
   boost::system::error_code&)  
{  
    u = boost::json::value_to<Event>(boost::json::parse(node.value));  
}  
```  
  
I have also defined these as a public function in my classes which matches one of my public constructors.  
```  
BOOST_DESCRIBE_CLASS(Bet, (), (), (), (fields..., event));  
BOOST_DESCRIBE_CLASS(Event, (), (), (), (fields...));  
```   
  
When trying to insert elements as shown in the example and the code below I get the following error.  
```  
    auto conn = std::make_shared<boost::redis::connection>(co_await boost::asio::this_coro::executor);  
    boost::redis::config cfg;  
    cfg.addr.host = "127.0.0.1";  
    cfg.addr.port = "6379";  
    conn->async_run(cfg, boost::redis::logger::level::warning, consign(boost::asio::detached, conn));  
    {  
        boost::redis::request request;  
        for (const Bet& bet : bets) {  
            request.push("SET", bet);  
        }  
        co_await conn->async_exec(request, boost::redis::ignore, boost::asio::deferred);  
    }  
    conn->cancel();  
```  
```  
In file included from Event.cpp:9:  
In file included from /opt/homebrew/include/boost/json/value_from.hpp:15:  
/opt/homebrew/include/boost/json/detail/value_from.hpp:151:9: error: static assertion failed due to requirement '!std::is_same<const Event &, const Event &>::value': No suitable tag_invoke overload found for the type  
  151 |         !std::is_same<T, T>::value,  
      |         ^~~~~~~~~~~~~~~~~~~~~~~~~~  
/opt/homebrew/include/boost/json/value_from.hpp:90:13: note: in instantiation of function template specialization 'boost::json::detail::value_from_impl<const Event &, boost::json::detail::no_context>' requested here  
   90 |     detail::value_from_impl( cat(), jv, std::forward<T>(t), ctx );  
  
In file included from Bet.cpp:9:  
In file included from /opt/homebrew/include/boost/json/value_from.hpp:15:  
/opt/homebrew/include/boost/json/detail/value_from.hpp:151:9: error: static assertion failed due to requirement '!std::is_same<const Bet &, const Bet &>::value': No suitable tag_invoke overload found for the type  
  151 |         !std::is_same<T, T>::value,  
      |         ^~~~~~~~~~~~~~~~~~~~~~~~~~  
/opt/homebrew/include/boost/json/value_from.hpp:90:13: note: in instantiation of function template specialization 'boost::json::detail::value_from_impl<const Event &, boost::json::detail::no_context>' requested here  
   90 |     detail::value_from_impl( cat(), jv, std::forward<T>(t), ctx );  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2025-04-29 09:22:36 UTC  
> Updated at: 2025-04-29 09:22:52 UTC  
> Url: https://github.com/boostorg/redis/issues/245#issuecomment-2838069798  

I've got the impression that the failure you're getting is at the Boost.Json level. Can you please try whether this compiles?  
  
```cpp  
boost::json::serialize(boost::json::value_from(Event{}));  
boost::json::value_to<Bet>(boost::json::parse(""));  
```  
  
Also, `boost_redis_from_bulk` should use the non-throwing `try_value_to`, instead.  
  
What version of Boost are you using?

---

## Comment 2

> Username: anarthal  
> Created at: 2025-09-08 20:37:31 UTC  
> Url: https://github.com/boostorg/redis/issues/245#issuecomment-3267931145  

This issue is stale so I'll be closing it now.
