# #329 - Does buffered_channel really need a power of two capacity ? [Open]

> Username: Romain-Geissler-1A  
> Created at: 2025-02-06 16:01:23 UTC  
> Updated at: 2025-02-06 16:01:23 UTC  
> Url: https://github.com/boostorg/fiber/issues/329  

Hi,  
  
In #111 you did write that the implementation of buffered_channel does rely on the fact that the capacity is a power of two. In the meantime, the implementation has changed, and from what I see now we have a more classical ring buffer with two points for the push and the pop location. So I don't see where the assumption that we have a power of two capacity still stands.  
  
Also the documentation says we can store only capacity - 1 elements in this channel (`The channel can hold only capacity - 1 elements, otherwise it is considered to be full`), but reading the code I do feel we can store exactly up to capacity elements.  
  
If I am right, I guess the documentation shall be updated to reflect these lack of constraints and the check for the capacity power of two caracteristic in the constructor of buffered_channel. That would make this "container" more like a "normal" one: it can really hold at max capacity elements (and not n - 1 as documented now) and can have arbitrary size.
