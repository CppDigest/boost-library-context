# #490 - thrown exceptions review [Open]

> Username: vinniefalco  
> Created at: 2022-09-01 22:40:20 UTC  
> Updated at: 2022-09-27 00:25:16 UTC  
> Url: https://github.com/boostorg/url/issues/490  

We need to throw `system_error` only, and document it correctly. Audit call sites.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2022-09-02 08:05:47 UTC  
> Url: https://github.com/boostorg/url/issues/490#issuecomment-1235197323  

But there are also user-supplied allocators , and you do not control what they throw.  
  
Also, I think that statements like "calls to allocator may throw" should become more specific: for each function potentially using an allocator, I would like to know under what condition the call to `allocate` will be performed. For instance, I want to have a guarantee that if I reserve sufficient memory ahead of time, no allocation will be made later.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-02 13:52:15 UTC  
> Url: https://github.com/boostorg/url/issues/490#issuecomment-1235532646  

> But there are also user-supplied allocators , and you do not control what they throw.  
  
There are no more user-supplied allocators.  
  
> I want to have a guarantee that if I reserve sufficient memory ahead of time, no allocation will be made later.  
  
Oh man... I just finished like a 16 hour marathon to rewrite all the javadocs for url_base and url_view_base...  
  
The pattern for all functions that reallocate the underlying buffer in `url` is very simple:  
  
1. measure  
2. reallocate  
3. encode  
  
There is only ever one reallocate performed (this helps ensure the strong exception safety guarantee). So in theory if you call `url_base::reserve(n)` where `n` is sufficiently large then it will not need to reallocate.  
  
The question then becomes what is the right value of `n`? Well, in the absence of encoding it is always equal to N-M where N is the size of the new part in characters and M is the size of the part it is replacing. But depending on the function you call it may be necessary to encode some characters, which will increase N beyond the size of the input string.  
  
I'm not sure this is worth editing every javadoc for... but also I don't know that I want to offer this guarantee as it will constrain changes to the implementation in the future.
