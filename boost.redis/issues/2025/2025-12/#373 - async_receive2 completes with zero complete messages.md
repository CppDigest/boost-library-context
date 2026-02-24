# #373 - async_receive2 completes with zero complete messages [Open]

> Username: mzimbres  
> Created at: 2025-12-14 14:58:19 UTC  
> Updated at: 2025-12-14 20:28:08 UTC  
> Url: https://github.com/boostorg/redis/issues/373  

While playing with valgrind massif I produced a condition where `async_receive2` completes with zero messages. I noticed this because `flat_tree::get_total_msgs()` was returning `0`. From looking at the code I could not spot the problem so I will have to investigate more.

---

## Comment 1

> Username: mzimbres  
> Created at: 2025-12-14 15:58:08 UTC  
> Url: https://github.com/boostorg/redis/issues/373#issuecomment-3651554119  

Found the problem: The push was arriving before I set the receive response and therefore it was being handled in the default adapter. I think this should be considered a misuse of the API: the receive response must be set before any pushes might possibly arrive i.e. before calling `async_run`.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-12-14 16:10:39 UTC  
> Url: https://github.com/boostorg/redis/issues/373#issuecomment-3651580222  

We could make the receive adapter part of config to prevent this, maybe?

---

## Comment 3

> Username: mzimbres  
> Created at: 2025-12-14 17:56:03 UTC  
> Url: https://github.com/boostorg/redis/issues/373#issuecomment-3651761082  

Yes, I think that is the correct thing to do. Also, I think `async_run` should exit with error if no receive adapter is set and a push is received.

---

## Comment 4

> Username: anarthal  
> Created at: 2025-12-14 18:39:11 UTC  
> Url: https://github.com/boostorg/redis/issues/373#issuecomment-3651799070  

We have some heuristics where under certain edge cases a regular message is interpreted as a push. I'm worried exiting with an error could do harm if these cases are hit and the user is not actually expecting pushes. Maybe we could issue a warning in the log?

---

## Comment 5

> Username: mzimbres  
> Created at: 2025-12-14 19:00:50 UTC  
> Url: https://github.com/boostorg/redis/issues/373#issuecomment-3651816393  

I think there is room to improve the heuristics, for example  
  
- Unsolicited errors (`-` and `!`) should not be sent over the receive channel but logged only.  
- The receive channel should only get pushes (`>`). This will make a custom message types for pushes more reliable and simpler to write i.e. no need to handle errors.  
- Any other resp3 type for which there is no corresponding request should cause an error and `async_run` to exit.

---

## Comment 6

> Username: anarthal  
> Created at: 2025-12-14 19:04:21 UTC  
> Url: https://github.com/boostorg/redis/issues/373#issuecomment-3651830345  

Wasn't MONITOR using a response type that wasn't a push (`>`)?

---

## Comment 7

> Username: mzimbres  
> Created at: 2025-12-14 19:35:51 UTC  
> Url: https://github.com/boostorg/redis/issues/373#issuecomment-3651879300  

Indeed, just check and it looks like it sends simple strings  
```  
"+1765740441.218176 [0 127.0.0.1:49960] \"ping\"\r\n"  
```  
This makes it harder to write custom response types since they have to accomodate for all possibilities. It is pretty hard to traverse a generic_response dealing with all kinds of responses.

---

## Comment 8

> Username: anarthal  
> Created at: 2025-12-14 20:28:08 UTC  
> Url: https://github.com/boostorg/redis/issues/373#issuecomment-3652001653  

Yeah, pretty evil. I don't think it's a common case, although we should probably still support it. I'll think of it. I agree the current status quo is not good.
