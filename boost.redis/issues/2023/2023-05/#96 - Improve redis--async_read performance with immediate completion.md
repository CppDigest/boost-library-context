# #96 - Improve redis::async_read performance with immediate completion? [Closed]

> Username: mzimbres  
> Created at: 2023-05-03 21:53:52 UTC  
> Updated at: 2023-05-30 21:10:04 UTC  
> Closed at: 2023-05-30 21:10:03 UTC  
> Url: https://github.com/boostorg/redis/issues/96  

Can the new ASIO support for immediate completion be used to improve performance of the `async_read_until`-`erase` idiom when there is data in the buffer from the last read?

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-05-30 21:10:03 UTC  
> Url: https://github.com/boostorg/redis/issues/96#issuecomment-1569107797  

Immediate executor won't help us here since it is a solution to ops the complete on the initiating function. Our problem is different: reduce the number of rescheduling needed to process a resp3 complete message. It is written somewhere by CK that custom executors can be used to promote throughput over fairness. I will close this ticket I research more.
