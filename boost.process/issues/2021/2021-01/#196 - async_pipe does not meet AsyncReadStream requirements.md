# #196 - async_pipe does not meet AsyncReadStream requirements [Open]

> Username: gulachek  
> Created at: 2021-01-21 15:32:24 UTC  
> Updated at: 2021-10-15 04:40:06 UTC  
> Url: https://github.com/boostorg/process/issues/196  

The async_pipe class does not meet the requirements for an [AsyncReadStream](https://www.boost.org/doc/libs/1_75_0/doc/html/boost_asio/reference/AsyncReadStream.html).  The get_executor() member should be implemented, probably to just return the executor of the underlying implementation.  The impact is that async operations following the guidelines of posting work on the associated executor of the operation will not compile since this member is not available.

---

## Comment 1

> Username: olk  
> Created at: 2021-01-22 09:54:04 UTC  
> Url: https://github.com/boostorg/process/issues/196#issuecomment-765285309  

I pushed pull request #197 in order to fix this issue.

---

## Comment 2

> Username: gulachek  
> Created at: 2021-10-15 04:40:05 UTC  
> Url: https://github.com/boostorg/process/issues/196#issuecomment-943991905  

I don't understand how using post() instead of dispatch() fulfills the request to add a get_executor() member function. 197 doesn't fix this.
