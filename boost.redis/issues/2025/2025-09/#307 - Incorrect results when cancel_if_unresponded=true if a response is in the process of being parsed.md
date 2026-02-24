# #307 - Incorrect results when cancel_if_unresponded=true if a response is in the process of being parsed [Open]

> Username: anarthal  
> Created at: 2025-09-15 17:12:03 UTC  
> Updated at: 2025-09-17 09:07:17 UTC  
> Url: https://github.com/boostorg/redis/issues/307  

I've written the following (failing) test:  
  
```cpp  
void test_cancel_on_connection_lost_half_parsed_response()  
{  
   // Setup  
   multiplexer mpx;  
   test_item item;  
   item.req.get_config().cancel_if_unresponded = false;  
   error_code ec;  
  
   // Add the request, write it and start parsing the response  
   mpx.add(item.elem_ptr);  
   BOOST_TEST_EQ(mpx.prepare_write(), 1u);  
   BOOST_TEST_EQ(mpx.commit_write(), 0u);  
   auto res = mpx.consume_next("*2\r\n+hello\r\n", ec);  
   BOOST_TEST_EQ(res.first, consume_result::needs_more);  
   BOOST_TEST_EQ(ec, error_code());  
  
   // Trigger a connection lost event  
   mpx.cancel_on_conn_lost();  
   BOOST_TEST(!item.done);  
   BOOST_TEST(item.elem_ptr->is_waiting());  
  
   // Simulate a reconnection  
   mpx.reset();  
  
   // Successful write, and this time the response is complete  
   BOOST_TEST_EQ(mpx.prepare_write(), 1u);  
   BOOST_TEST_EQ(mpx.commit_write(), 0u);  
   res = mpx.consume_next("*2\r\n+hello\r\n+world\r\n", ec);  
   BOOST_TEST_EQ(res.first, consume_result::got_response);  
   BOOST_TEST_EQ(ec, error_code());  
  
   // Check the response  
   BOOST_TEST(item.resp.has_value());  
   const node expected[] = {  
      {type::array,         2u, 0u, ""     },  
      {type::simple_string, 1u, 1u, "hello"},  
      {type::simple_string, 1u, 1u, "world"},  
   };  
   BOOST_TEST_ALL_EQ(  
      item.resp->begin(),  
      item.resp->end(),  
      std::begin(expected),  
      std::end(expected));  
}  
```  
  
In essence, add a request, write it successfully and start parsing its response. In the middle of this process, a connection loss happens. The adapter has already some data, which can't get discarded because adapters don't support it. Reconnection succeeds and the request is sent successfully and its response arrives. The final response contains the incomplete response received the first time, plus the complete one received the second time.  
  
I don't think solving this is super important right now, but should be done at some point. We should probably disable unconditionally cancel the request (even if `cancel_if_unresponded=true`) if a response has received some data already. We could also think of something different, since `cancel_if_unresponded`, `cancel_if_not_connected` and `cancel_on_connection_lost` seem error-prone.

---

## Comment 1

> Username: criatura2  
> Created at: 2025-09-16 12:38:09 UTC  
> Url: https://github.com/boostorg/redis/issues/307#issuecomment-3298568469  

> In essence, add a request, write it successfully and start parsing its response. In the middle of this process, a connection loss happens. The adapter has already some data, which can't get discarded because adapters don't support it. Reconnection succeeds and the request is sent successfully and its response arrives. The final response contains the incomplete response received the first time, plus the complete one received the second time.  
  
The adapters receive events in the order `on_init`, `on_node` and `on_done`.  This can be used to discard data when `cancel_if_unresponded` is `false`, for example, if `on_init` is called twice or does not have a corresponding `on_done`.  
  
The adapter constructors such as [this](https://github.com/boostorg/redis/blob/40417a13b2c140dd5e15ffa31bf2a80cbe72b018/include/boost/redis/adapter/detail/adapters.hpp#L543) one could store `cancel_if_unresponded` to decide if data is discarded or an error is issued. Adapters that can receive multiple responses such as the `generic_response` would have to keep the position of the last successful response and discard only the incomplete responses.  
  
It might be also possible that the rest of the response is already locally available in kernel space and just not read from the socket. Perhaps we should read until no data is available before closing the socket and reseting, this way we avoid resending the request and discarding the data.  
  
> I don't think solving this is super important right now, but should be done at some point. We should probably disable unconditionally cancel the request (even if cancel_if_unresponded=true) if a response has received some data already. We could also think of something different, since cancel_if_unresponded, cancel_if_not_connected and cancel_on_connection_lost seem error-prone.  
  
The protocol alone can't prevet double execution of commands so we have to handle it anyway. I think the library shouldn't make any decision on behalf of the user but give them the option for whatever behaviour makes more sense.  
  
These flags arose very early in the Boost.Redis development when I was writing a chat server.  If we know the client handles reconnections and failover properly I can retrieve user messages from the server with simple code like this  
  
```cpp  
  response resp;  
  request req{{.cancel_if_unresponded = false}};  
  req.push(/*commands to retrieve the message*/)  
  
  co_await conn.async_exec(req, resp);  
```  
  
On the other hand if I know `async_exec` might get messed up if caught on a sudden disconnect/failover, I would have to write code like this instead  
  
```cpp  
response resp;  
request req{{.cancel_if_unresponded = false}};  
req.push(/*commands to retrieve the message*/)  
  
for (error_code ec;;) {  
    co_await conn.async_exec(req, resp, redirect_error(deferred, ec));  
    if (ec == error::residual_data_found) {  
        ec= {};  
        continue;  
    } else {  
        break;  
    }  
}  
```  
  
or similar, which is error prone and a burden to the user. I don't think users will get this correctly most of the time.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-09-16 14:35:39 UTC  
> Url: https://github.com/boostorg/redis/issues/307#issuecomment-3299062136  

I see. It makes sense. I need to get more familiar with adapters before I fix this then.

---

## Comment 3

> Username: anarthal  
> Created at: 2025-09-16 14:38:40 UTC  
> Url: https://github.com/boostorg/redis/issues/307#issuecomment-3299081519  

There's still something I don't get about these flags: why is the distinction between cancel_if_not_connected and cancel_on_connection_lost made? They also default to different values. In both cases, it's about "do you want to wait for the connection to be up"?

---

## Comment 4

> Username: criatura2  
> Created at: 2025-09-17 09:07:17 UTC  
> Url: https://github.com/boostorg/redis/issues/307#issuecomment-3302048651  

> There's still something I don't get about these flags: why is the distinction between cancel_if_not_connected and cancel_on_connection_lost made? They also default to different values. In both cases, it's about "do you want to wait for the connection to be up"?  
  
#### `cancel_if_not_connected`  
  
In essense this means "_cancel if the socket is closed_"  
  
```cpp  
if (elem_->get_request().get_config().cancel_if_not_connected && !connection_is_open) {  
   BOOST_REDIS_YIELD(resume_point_, 1, exec_action_type::immediate)  
   elem_.reset();  // Deallocate memory before finalizing  
   return system::error_code(error::not_connected);  
}  
```  
  
Here is a user asking for it https://github.com/boostorg/redis/issues/164.  
  
This flag will become unnecessary once cancelation for `async_exec` is working and we will have to deprecate it. `cancel_after` will be the recommended replacement. We've had a short discussion about it [here](https://github.com/boostorg/redis/issues/236). The issue for `cancel_after` is here https://github.com/boostorg/redis/issues/226.  
  
#### `cancel_on_connection_lost`  
  
This gives users the option to avoid retries if the connection is lost, namely, it will wait until a connection is stablished but exit with an error if the connection is lost and the request hasn't been sent. I am not sure anymore but this might have been a response to @ashtum [review](https://listarchives.boost.org/Archives/boost/2023/01/253929.php)  
  
> The connection being reconnectable seems a design flaw:  
>  
> The idea behind having a reconnectable connection seems to be there to make the user's life easier (by keeping the user's request and retrying them on reconnect) but there are some issues:  
>  
> A network disconnect leaves the user's request in an undefined state (they might have applied on the server or not) we can't simply retry them after a reconnection it requires the user's intervention to handle a reconnect. For example, we can't simply retry an INCRBY command, it might increment the value twice.  
>  
> Because of the possibility of reconnecting the handshake process has to be done by users (by sending HELLO command as the first request) and on a reconnect this should be repeated again, this affected the implementation in a way it has to check for each command to see if it is a HELLO command or not.  
  
By default there is no retry even if the request hasn't been sent (when there is no risk of double execution). At the moment I think the only essential flag is `cancel_if_unresponded` so perhaps we can also drop `cancel_on_connection_lost`.
