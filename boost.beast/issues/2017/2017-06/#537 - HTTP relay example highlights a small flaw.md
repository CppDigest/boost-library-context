# #537 - HTTP relay example highlights a small flaw [Closed]

> Username: vinniefalco  
> Created at: 2017-06-24 16:21:07 UTC  
> Updated at: 2022-10-12 00:55:58 UTC  
> Closed at: 2022-10-12 00:55:58 UTC  
> Url: https://github.com/boostorg/beast/issues/537  

The way that the relay shares the message between the `serializer` and the `parser` is brittle.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-10-12 00:55:58 UTC  
> Url: https://github.com/boostorg/beast/issues/537#issuecomment-1275447578  

I reckon the missing const enforcement in the `relay` function in `http_relay.hpp` is the issue. I assume this is not really a big issue? Seems like little payoff for major work.
