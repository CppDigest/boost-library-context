# #1513 - stronger contract on flat_buffer, flat_static_buffer [Closed]

> Username: vinniefalco  
> Created at: 2019-03-08 21:02:57 UTC  
> Updated at: 2024-01-05 09:01:23 UTC  
> Closed at: 2024-01-05 09:01:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1513  

Instead of:  
  
> Buffer sequences returned by basic_flat_buffer::data and basic_flat_buffer::prepare will always be of length one.  
  
We can make a stronger statement:  
  
> The type of buffer sequence returned will be `net::const_buffer` or `net::mutable_buffer`

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-09-24 06:03:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1513#issuecomment-1256875670  

^ This should also take into account the dynamic_buffer developments in boost .asio

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-25 00:30:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1513#issuecomment-1257090459  

We have tried to update Beast's usage of dynamic buffers to match the style in Asio and the result has only been extreme pain and breakage. I think I have just accepted that Beast's dynamic buffers are going to be different than the ones in Asio. If anything, we should make this more clear in the documentation.  
  
In my new HTTP library, I am avoiding the use of dynamic buffers entirely, so this becomes a non-problem.
