# #323 - Should cancel_on_connection_lost be false by default? [Closed]

> Username: anarthal  
> Created at: 2025-10-03 18:20:53 UTC  
> Updated at: 2025-10-11 11:30:42 UTC  
> Closed at: 2025-10-11 11:30:42 UTC  
> Url: https://github.com/boostorg/redis/issues/323  

I'd like to discuss the possibility of changing `request::config::cancel_on_connection_lost` default to `false`. The reason being that I see both `cancel_if_not_connected` and `cancel_on_connection_lost` as the same thing - answering "do I want to make this request fail ASAP, or should I try to wait until the server is up?". `cancel_if_not_connected` defaults to false, while `cancel_on_connection_lost` defaults to true, which makes it confusing IMO.  
  
These are very handy for tests, but IDK if they've got any real usage now that we're about to have support for `cancel_after`. Maybe we could deprecate the flags in addition to changing the default.  
  
Note that I don't think this change will break a lot of users, if any.

---

## Comment 1

> Username: mzimbres  
> Created at: 2025-10-03 19:32:14 UTC  
> Url: https://github.com/boostorg/redis/issues/323#issuecomment-3366965208  

- `cancel_if_not_connected`: We should deprecate and remove this flag now that cancellation is implemented.  
- `cancel_on_connection_lost`: Yes, we can change the default. But actually we should also deprecate and remove this flag too. As I explained elsewhere, it was added to address Mohammed's concern about the side effects of automatic reconnection, which I think are not justifiable. The connection abstracts reconnection completely away and in a safe way (with the `cancel_if_unresponded`) from the user. I think even in a scenario with sentinel this is unnecessary i.e. a request is only meant to be executed on the master but not on a slave, in which case the user does not want it to survive a reconnection. But the likelihood this is really needed it pretty low i.e. just open a new connection with reconnection disabled.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-10-03 20:00:21 UTC  
> Url: https://github.com/boostorg/redis/issues/323#issuecomment-3367044505  

Great. I'll deprecate them and change the default. They're heavily used in tests, so it's possible that we can't get rid of the internals that easily. But that's a long time from now, so let's not worry yet.
