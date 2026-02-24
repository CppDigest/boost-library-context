# #50 - Subscriber error reported on the Boost review [Closed]

> Username: mzimbres  
> Created at: 2023-01-21 16:55:04 UTC  
> Updated at: 2023-01-29 20:16:09 UTC  
> Closed at: 2023-01-29 20:16:09 UTC  
> Url: https://github.com/boostorg/redis/issues/50  

> 2. I've modified cpp20_subscriber to add another task that sends commands  
>     with async_exec while reading subscription replies. If any of the commands  
>     issued with async_exec contain any errors (like the LPOP above), the program  
>     crashes with a "conn->cmds_ != 0" assertion.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-01-22 13:57:09 UTC  
> Url: https://github.com/boostorg/redis/issues/50#issuecomment-1399494964  

Hi, this is the code I used:  
  
https://gist.github.com/anarthal/f84bfe4864b5bc49561af0eeabb45d3f  
  
You need to have `mykey` not set (so that the periodic GET fails) and then send a push to `channel`.  
  
The assertion is triggered in case of error, that is, if the key `mykey` isn't set. As I understood it, the parsing fails because of a type mismatch (I should have passed a `std::optional<std::string>`), which causes the response not to be consumed somehow (I'm not familiar enough to say more). When the push arrives, it attempts the heuristic rule you have to determine edge case pushes, and that triggers the assertion. Again, that's just intuition, I haven't debugged it.  
  
Hope it helps,  
Ruben.

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-01-28 08:48:59 UTC  
> Url: https://github.com/boostorg/redis/issues/50#issuecomment-1407343027  

Fixed in https://github.com/mzimbres/aedis/commit/56c0b280033886510e3f91f4704f4ed87da4a14d along with other improvements. The problem is that the read buffer was not being cleaned up before reconnecting. It is left untouched after the disconnection to make post-mortem debugging possible but before reconnection it must be cleaned up.
