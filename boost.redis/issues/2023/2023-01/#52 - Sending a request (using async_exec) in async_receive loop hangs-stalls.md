# #52 - Sending a request (using async_exec) in async_receive loop hangs/stalls [Closed]

> Username: jsaf0  
> Created at: 2023-01-24 10:48:36 UTC  
> Updated at: 2023-01-28 14:32:43 UTC  
> Closed at: 2023-01-28 14:32:43 UTC  
> Url: https://github.com/boostorg/redis/issues/52  

Hi @mzimbres,  
  
I have made a small example where I receive messages published to a channel ( `/my-channel` in this example). Upon receiving a message, I issue a HGETALL request to fetch some data from a hash.  
  
If another message is received immediately after, the `async_exec` with the HGETALL requests hangs forever.  
  
Receiver routine:  
  
```c++  
auto receiver(shared_ptr<aedis::connection> conn) -> asio::awaitable<void>  
{  
    aedis::resp3::request sub_req;  
    sub_req.push("SUBSCRIBE", "/my-channel");  
    co_await conn->async_exec(sub_req, aedis::adapt(), asio::use_awaitable);  
  
    for (;;) {  
        std::vector<aedis::resp3::node<std::string>> resp;  
        for (;;) {  
            co_await conn->async_receive(aedis::adapt(resp), asio::use_awaitable);  
            resp.clear();  
  
            map<string, string> values;  
            auto resp2 = std::tie(values);  
            aedis::resp3::request req;  
            req.push("HGETALL", "/my-hash");  
            co_await conn->async_exec(req, aedis::adapt(resp2), asio::use_awaitable);              
        }  
    }  
}  
```  
  
I publish two times to the channel right after each other.  
`echo -e "PUBLISH /my-channel 1\nPUBLISH /my-channel 2" | redis-cli`  
  
Full example code: https://gist.github.com/jsaf0/30c1a32b5208716e3c531b1d389be9c4

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-01-24 19:28:35 UTC  
> Url: https://github.com/boostorg/redis/issues/52#issuecomment-1402479081  

This is not clear in the documentation but you cannot call `async_exec` and `async_receive` in a way where they can block each other. In this case you are probably receiving a push while suspended on `HGETALL`. The are many workarounds, the simplest is perhaps to use a second connection. You can also put `async_receive` and `async_exec` in different coroutines and then use a queue and a timer to communicate between both, when a push is received you push it to queue and cancel the timer the other coroutine is waiting on, than pop the queue and execute the command.

---

## Comment 2

> Username: jsaf0  
> Created at: 2023-01-25 09:29:38 UTC  
> Url: https://github.com/boostorg/redis/issues/52#issuecomment-1403321260  

Thanks, @mzimbres. As you say, this is not clear when reading the documentation or looking in the examples. We'll continue with one of the options (most likely two or more coroutines to keep it at one connection).

---

## Comment 3

> Username: mzimbres  
> Created at: 2023-01-28 08:51:11 UTC  
> Url: https://github.com/boostorg/redis/issues/52#issuecomment-1407343470  

> most likely two or more coroutines to keep it at one connection  
  
You just have to be careful when using queues like this as they won't offer automatic back-pressure, that's another thing though. Can we close this ticket?
