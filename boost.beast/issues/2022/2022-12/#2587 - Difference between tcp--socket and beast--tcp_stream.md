# #2587 - Difference between tcp::socket and beast::tcp_stream [Closed]

> Username: klemens-morgenstern  
> Created at: 2022-12-13 15:34:03 UTC  
> Updated at: 2022-12-14 16:49:13 UTC  
> Closed at: 2022-12-13 15:53:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2587  

### Discussed in https://github.com/boostorg/beast/discussions/2581  
  
<div type='discussions-op-text'>  
  
<sup>Originally posted by **pfeatherstone** December 13, 2022</sup>  
I think `tcp::socket` can have a polymorphic executor too now. Is there a reason to prefer `beast::tcp_stream`?</div>

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-12-13 15:53:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2587#issuecomment-1348872834  

Timeuts  & rate limiting

---

## Comment 2

> Username: pfeatherstone  
> Created at: 2022-12-14 09:25:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2587#issuecomment-1350712022  

@klemens-morgenstern  Thank you very much for your answer. I appreciate the information was there already in the docs, but i feel like both Asio and Beast have moved on from the original docs. I imagine when `tcp_stream` was first written, polymorphic executors weren't supported yet in `tcp::socket` ? So i wanted to check just in case the beast docs hadn't been updated since the latest releases of Asio.  
  
So for timeouts we can use `deadline_timer` from Asio right? Though i can see `tcp_stream` makes this a bit easier.   
Rate limiting, I would have to dig the docs to see why I would want to do that.   
  
But thanks again.

---

## Comment 3

> Username: pfeatherstone  
> Created at: 2022-12-14 09:25:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2587#issuecomment-1350713685  

Also, any reason why github discussions was disabled? It's a good platform to ask Asio and Beast related questions

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-12-14 16:49:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2587#issuecomment-1351758454  

I don't like splitting questions into two separate areas instead of one. Just ask in Issues.
