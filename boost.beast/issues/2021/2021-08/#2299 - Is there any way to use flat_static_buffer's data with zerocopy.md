# #2299 - Is there any way to use flat_static_buffer's data with zerocopy? [Closed]

> Username: JaydenFish  
> Created at: 2021-08-17 03:27:47 UTC  
> Updated at: 2021-08-17 06:04:55 UTC  
> Closed at: 2021-08-17 06:04:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2299  

I receive network data with flat_static_buffer or static_buffer.   
Is there any better way than boost::beast::buffers_to_string(static_buffer.data())? Making a string is expensive because of heap allocation.  
I'm using boost1.66.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-08-17 05:35:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2299#issuecomment-900006936  

Since these buffers have a contiguous memory layout it is straightforward to simply get access to the underlying data and view it through (for example) a string_view.  
  
e.g.  
  
    auto mem = mybuff.data();  
    auto sv = string_view(reinterpret_cast<const char *>(mem.data()), mem.size());

---

## Comment 2

> Username: JaydenFish  
> Created at: 2021-08-17 06:04:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2299#issuecomment-900017661  

thank you very much!
