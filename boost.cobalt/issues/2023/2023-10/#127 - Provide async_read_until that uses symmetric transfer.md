# #127 - Provide async_read_until that uses symmetric transfer [Open]

> Username: mzimbres  
> Created at: 2023-10-03 07:05:10 UTC  
> Updated at: 2023-10-04 23:31:28 UTC  
> Url: https://github.com/boostorg/cobalt/issues/127  

A coroutine based implementation of `asio::async_read_until` should be more efficient than the Asio version since it can use symmetric-transfer to avoid a reschedule operation when the buffer already contain the separator.  
  
It would be nice if you could provide this as an example. It would also be an opportunity to show users what kind of improvement they get in comparison to Asio. I would also include this in the benchmark session.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-10-04 23:31:28 UTC  
> Url: https://github.com/boostorg/cobalt/issues/127#issuecomment-1747787450  

Good idea, but I might just add my own impl if I do some IO work.
