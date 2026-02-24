# #367 - Pubsub state restoration [Closed]

> Username: anarthal  
> Created at: 2025-12-02 10:50:58 UTC  
> Updated at: 2026-01-09 20:08:55 UTC  
> Closed at: 2026-01-09 20:08:55 UTC  
> Url: https://github.com/boostorg/redis/issues/367  

Currently, every time a reconnection happens, we cancel `async_receive2` so the user has a chance to re-issue all the SUBSCRIBE commands. This is not very user-friendly. Other clients do this automatically.  
  
The easiest way would be adding the SUBSCRIBE commands to the setup request, but this wouldn't allow dynamically subscribing/unsubscribing to channels. So we need another mechanism to cover this.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-12-02 10:58:58 UTC  
> Url: https://github.com/boostorg/redis/issues/367#issuecomment-3601440065  

Options:  
  
1. Add a `replay_on_reconnection` flag to `request`. When a reconnection happens, the request is executed again. This is simple but imposes no bound on the number of requests that get executed on reconnection. E.g. if a process issues `SUBSCRIBE foo`, then `UNSUBCRIBE foo`, then `SUBSCRIBE bar`, we will run 3 commands, instead of just 1.  
2. Add a `restore_pubsub` flag to `request`. If the flag is set and the request contains SUBSCRIBE or UNSUBSCRIBE messages, we track which channels it's targeting. We store these in a map, and compose a big SUBSCRIBE request whenever a reconnection happens. This requires inspecting the arguments passed to `push` and `push_range`, and that's a problem because the customization point `boost_redis_to_bulk` doesn't allow access to the request object.  
3. Inspect the received pushes, looking for `"subscribe"` and `"unsubscribe"` messages, and build a map using this. This may be subject to race conditions: the user executes a `SUBSCRIBE` request, which succeeds because it has no response, but we never end up receiving the `"subscribe"` push message (e.g. network fault), so the subscription would be "lost".  
  
@mzimbres thoughts?
