# #3066 Avoid tyecast warnings in `inflate_with_eb` [Merged]

> Username: pps83  
> Created at: 2026-01-07 21:38:46 UTC  
> Updated at: 2026-01-25 13:23:27 UTC  
> Merged at: 2026-01-25 12:36:18 UTC  
> Closed at: 2026-01-25 12:36:18 UTC  
> Url: https://github.com/boostorg/beast/pull/3066  



---

## Review 1 [Commented]

> Username: pps83  
> Created_at: 2026-01-07 21:40:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3066#pullrequestreview-3636902462  

fixes warnigs like this:  
```  
1>C:\boost_1_90_0\boost\beast\websocket\detail\impl_base.hpp(186,35): warning C4267: '+=': conversion from 'size_t' to 'uint8_t', possible loss of data  
1>  (compiling source file '../src/test/WebSocketTest.cpp')  
```

---

## Comment 2

> Username: ashtum  
> Created_at: 2026-01-08 07:00:27 UTC  
> Url: https://github.com/boostorg/beast/pull/3066#issuecomment-3722373465  

The logic is correct, `rd_eb_consumed` can never be greater than 4:  
```C++  
        const std::uint8_t eb[4] = { 0x00, 0x00, 0xff, 0xff };  
        zs.next_in = eb + pmd_->rd_eb_consumed;  
        zs.avail_in = sizeof(eb) - pmd_->rd_eb_consumed;  
        inflate(zs, ec);  
        pmd_->rd_eb_consumed += zs.total_in;  
        BOOST_ASSERT(pmd_->rd_eb_consumed <= sizeof(eb));  
        if(ec == zlib::error::need_buffers)  
            ec.clear();  
```  
Changing `std::uint8_t` to `std::size_t` would silence the warning, but it does not appear to provide additional safety. If the value were ever to exceed 4, the behavior would already be undefined. For that reason, adding a `static_cast` on the following line seems like a more appropriate approach:  
```C++  
        pmd_->rd_eb_consumed += zs.total_in;  
```

---

## Comment 3

> Username: pps83  
> Created_at: 2026-01-10 15:28:55 UTC  
> Updated_at: 2026-01-10 15:39:18 UTC  
> Url: https://github.com/boostorg/beast/pull/3066#issuecomment-3733035703  

> The logic is correct, `rd_eb_consumed` can never be greater than 4:  
  
this part is **very** not obvious. What makes it correct? The assert? That `inflate` works this way now? I would not put that dependency into the code because there is no benefit/reason for doing so.  
  
There is no benefit to use `uint8_t`, these sized types shouldn't be used just because something fits in a smaller sized type. On top of all that, the struct that has that `rd_eb_consumed` perhaps is 10KB in size.  
  
> Changing std::uint8_t to std::size_t would silence the warning, but it does not appear to provide additional safety.   
  
That's not about safety. There is a warning because consumed size is accumulated to a byte instead of a type meant to store sizes. Simple as that.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2026-01-10 15:43:49 UTC  
> Url: https://github.com/boostorg/beast/pull/3066#issuecomment-3733057525  

`eb` only has 4 elements so how could eb_consumed be greater than 4?

---

## Comment 5

> Username: pps83  
> Created_at: 2026-01-10 17:24:32 UTC  
> Updated_at: 2026-01-10 17:25:17 UTC  
> Url: https://github.com/boostorg/beast/pull/3066#issuecomment-3733254151  

> `eb` only has 4 elements so how could eb_consumed be greater than 4?  
  
this part is not obvious. Even if it cannot be more than 4, there is no benefit of using uint8_t: `struct pmd_type` that contains `eb_consumed` is huge as it stores zlib state that should be well over 10KB in size (I didn't check, but I'm fairly sure it should be large).  
  
If you think it's better to cast, let me know and I'll update the patch to a cast instead.  
  
In any case, I think it should be `size_t` though. As `how could eb_consumed be greater than 4` depends on `inflate` (which is a function outside of beast afaik). In short, one may use some custom `inflate` code that could do something else/unexpected.

---

## Comment 6

> Username: ashtum  
> Created_at: 2026-01-22 11:00:51 UTC  
> Updated_at: 2026-01-22 12:13:38 UTC  
> Url: https://github.com/boostorg/beast/pull/3066#issuecomment-3783792180  

> this part is **very** not obvious. What makes it correct? The assert? That `inflate` works this way now? I would not put that dependency into the code because there is no benefit/reason for doing so.  
  
If `rd_eb_consumed` contain anything more than 4 it would lead to an out of bound access which is a bigger issue (I'm not suggesting that's why I chose uint8_t over size_t though).   
  
>   
> There is no benefit to use `uint8_t`, these sized types shouldn't be used just because something fits in a smaller sized type. On top of all that, the struct that has that `rd_eb_consumed` perhaps is 10KB in size.  
>   
> > Changing std::uint8_t to std::size_t would silence the warning, but it does not appear to provide additional safety.  
>   
> That's not about safety. There is a warning because consumed size is accumulated to a byte instead of a type meant to store sizes. Simple as that.  
  
I would have fully agreed with you if this were a user-facing interface. Even so, I still agree with your point, given that `struct pmd_type` is already quite large, the additional 8 bytes would make no practical difference. So, I don’t think the PR needs any further refinements.  
  
Thank you.

---
