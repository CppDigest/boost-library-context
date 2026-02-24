# #460 Fix C4242 warning in serial_port_base::character_size::store [Open]

> Username: ssam18  
> Created at: 2025-11-12 21:03:44 UTC  
> Updated at: 2025-12-01 20:53:45 UTC  
> Url: https://github.com/boostorg/asio/pull/460  

This fixes #392 where MSVC was throwing a C4242 warning about converting from unsigned int to BYTE.  
  
I hit this issue when compiling with /WX (treat warnings as errors). The warning happens because value_ is unsigned int but storage.ByteSize expects a BYTE.  
  
The fix is simple - just add a static_cast<BYTE>. The cast is safe since character_size values are always in the valid range (5, 6, 7, or 8 bits).  
  
Tested that this compiles cleanly on MSVC 2019 with /W4.

---

## Comment 1

> Username: ssam18  
> Created_at: 2025-11-30 14:50:04 UTC  
> Url: https://github.com/boostorg/asio/pull/460#issuecomment-3592625646  

Can someone take a look on this PR?

---

## Comment 2

> Username: nigels-com  
> Created_at: 2025-12-01 08:46:26 UTC  
> Url: https://github.com/boostorg/asio/pull/460#issuecomment-3595313886  

Looks good to me.

---

## Comment 3

> Username: SamareshSingh  
> Created_at: 2025-12-01 20:53:45 UTC  
> Url: https://github.com/boostorg/asio/pull/460#issuecomment-3598784890  

@nigels-com - Thank you for reviewing this PR. I am going to pick up another issue to work on. If you have something that is urgent/important, please pass it on my way. I will work on that.

---
