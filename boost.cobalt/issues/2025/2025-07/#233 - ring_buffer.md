# #233 - ring_buffer [Open]

> Username: klemens-morgenstern  
> Created at: 2025-07-04 12:56:27 UTC  
> Updated at: 2025-07-04 12:56:27 UTC  
> Url: https://github.com/boostorg/cobalt/issues/233  

A ring_buffer could be useful in situations where one wants a channel but back-pressure would be an issue.  
  
It should have the same API as a channel.  
  
On write, the writing coroutine should be yield to a reading coroutine if present.  
If the ring-buffer is full the awaiting coroutine should post it-self to avoid starvation.  
  
The ring_buffer could also have a `post` function, that posts a value and then the completion.   
This function could also be thread-safe (since it posts internally) allowing for cross-thread notifications.  
  
`T = void` would be a notifier, especially when of size 1.
