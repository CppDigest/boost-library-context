# #2929 Websocket continuation frames dropped if there are leftovers in the buffer from the previous read [Closed]

> Username: pyrist  
> Created at: 2024-09-11 10:51:27 UTC  
> Updated at: 2024-09-12 13:07:27 UTC  
> Closed at: 2024-09-12 13:06:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2929  

Fix for #2928

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-09-11 11:03:23 UTC  
> Url: https://github.com/boostorg/beast/pull/2929#issuecomment-2343325146  

An automated preview of the documentation is available at [https://2929.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2929.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-09-11 11:52:16 UTC  
> Updated_at: 2024-09-12 13:07:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2929#issuecomment-2343456863  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2929?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.10%. Comparing base [(`3ddcf4e`)](https://app.codecov.io/gh/boostorg/beast/commit/3ddcf4ed381a68d95558e29ca20e23a9856cd753?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`6b2f84d`)](https://app.codecov.io/gh/boostorg/beast/commit/6b2f84d03713191903969788afd9219f1e36fa91?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2929/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2929?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2929   +/-   ##  
========================================  
  Coverage    93.10%   93.10%             
========================================  
  Files          177      177             
  Lines        13656    13657    +1       
========================================  
+ Hits         12714    12715    +1       
  Misses         942      942             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/beast/pull/2929?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2929?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Fread.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `94.65% <100.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2929?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2929?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3ddcf4e...6b2f84d](https://app.codecov.io/gh/boostorg/beast/pull/2929?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2024-09-11 15:35:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2929#issuecomment-2344012779  

Is this a real bug?

---

## Comment 4

> Username: pyrist  
> Created_at: 2024-09-11 15:58:53 UTC  
> Updated_at: 2024-09-11 15:59:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2929#issuecomment-2344065639  

> Is this a real bug?  
  
I believe so. It wouldn't make sense to me that the read loop should end after reading part of a header frame when the message and frame aren't finished.   
  
I've also looked everywhere to find any other causes of the behavior and narrowed it down to this.

---

## Comment 5

> Username: ashtum  
> Created_at: 2024-09-11 18:59:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2929#issuecomment-2344450098  

@pyrist, I couldn't reproduce the situation you described with the following code, which tests various message sizes and different points for chopping the input bytes.  
```C++  
for(std::size_t msg_size = 1 ; msg_size <= 10000; msg_size += 7)  
{  
    for(std::size_t i = 0 ;; ++i)  
    {  
        net::io_context ioc;  
        stream<test::stream> wsc{ioc};  
        stream<test::stream> wss{ioc};  
        wsc.next_layer().connect(wss.next_layer());  
        wsc.async_handshake(  
            "localhost", "/", [](error_code){});  
        wss.async_accept([](error_code){});  
        ioc.run();  
        ioc.restart();  
        BEAST_EXPECT(wsc.is_open());  
        BEAST_EXPECT(wss.is_open());  
        auto sent_msg = std::string(msg_size, 'a');  
        wss.write(net::buffer(sent_msg));  
  
        if(i > wsc.next_layer().str().size())  
            break; // we have tested all possible chunk sizes  
  
        // steal the input buffer in order to write it in two steps  
        std::string bytes = wsc.next_layer().str();  
        wsc.next_layer().buffer().clear();  
  
        // first part  
        wss.next_layer().write_some(  
            net::buffer(bytes.substr(0, i)));  
  
        flat_buffer received_msg;  
        wsc.async_read(  
            received_msg,  
            [](error_code, std::size_t){});  
  
        ioc.poll();  
  
        // second part  
        wss.next_layer().write_some(  
            net::buffer(bytes.substr(i)));  
  
        ioc.run();  
        BEAST_EXPECT(received_msg.size() == msg_size);  
    }  
}  
```

---

## Comment 6

> Username: pyrist  
> Created_at: 2024-09-11 19:10:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2929#issuecomment-2344487429  

@ashtum Thanks for looking at it. I will look tomorrow to compare between what your code is doing and what is going wrong when I use it and see if I can make something reproducible.

---

## Review 7 [Commented]

> Username: ashtum  
> Created_at: 2024-09-11 19:17:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2929#pullrequestreview-2298409356  

📁 include/boost/beast/websocket/impl/read.hpp

```diff
 519 |+                 }
 520 |+                 else {
 521 |+                     cont = true;
```

> Username: ashtum  
> Created_at: 2024-09-11 19:17:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2929#discussion_r1755433138  

`cont` is unrelated to the logic of the read operation itself; it determines whether the completion handler should be scheduled on an executor or executed directly. I suspect there might be an issue in your custom stream type, where an additional dispatch can make it work.


---

## Comment 8

> Username: pyrist  
> Created_at: 2024-09-12 13:06:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2929#issuecomment-2346235538  

@ashtum Thanks so much for the help. I used your example to compare what was going on with our sockets. You're right, our socket failing to call an additional dispatch is what's actually going on here, and setting cont to true just forces beast to do the socket's job for it.

---
