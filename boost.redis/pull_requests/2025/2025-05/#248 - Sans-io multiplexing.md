# #248 Sans-io multiplexing [Merged]

> Username: mzimbres  
> Created at: 2025-05-05 20:12:36 UTC  
> Updated at: 2025-05-10 18:52:10 UTC  
> Merged at: 2025-05-10 18:52:10 UTC  
> Closed at: 2025-05-10 18:52:10 UTC  
> Url: https://github.com/boostorg/redis/pull/248  

@anarthal This still needs a lot of improvements but even in this state it is better than before. The multiplexer has been factored out of the connection and some tests have been added. There are lots of corner cases that can be tested now.  
  
I think reviewing this might be overwhelming but looking at the tests you will get an idea about how the multiplexer works. Let me know if you have any questions.

---
