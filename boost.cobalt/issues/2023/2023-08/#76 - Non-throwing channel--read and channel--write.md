# #76 - Non-throwing channel::read and channel::write [Closed]

> Username: anarthal  
> Created at: 2023-08-15 19:55:46 UTC  
> Updated at: 2024-05-01 12:09:46 UTC  
> Closed at: 2024-05-01 12:09:46 UTC  
> Url: https://github.com/boostorg/cobalt/issues/76  

Would it be possible to have functions that don't throw, but return an error code, on cancellation?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-16 00:17:00 UTC  
> Url: https://github.com/boostorg/cobalt/issues/76#issuecomment-1679790003  

There are a few solutions  
  
1. overload that's non-throwing  
2. make interrupt return a default constructed value if it's trivial  
3. make all of async `system::result` aware.

---

## Comment 2

> Username: anarthal  
> Created at: 2023-08-16 18:45:29 UTC  
> Url: https://github.com/boostorg/cobalt/issues/76#issuecomment-1681108333  

2. sounds bad. 1. and 3. sound better. 3. is the one that sounds best.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-08-17 01:21:15 UTC  
> Url: https://github.com/boostorg/cobalt/issues/76#issuecomment-1681468662  

What's bad about (2) ? The value gets ignored anyhow, so a `channel<int>` could just return 0.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-08-17 14:17:27 UTC  
> Url: https://github.com/boostorg/cobalt/issues/76#issuecomment-1682370058  

`interrupt_await` could do (2) when used with select. Value gets ignored. If of course can't do that with regular read/write.  
  
(1) would not work for non-default ctor-able T, so I think I should  
  
add (2) for interrupt await & (3) for channel's only. I don't think stuff like `promise<result<void, std::exception_ptr>>` is necessary for now, but might be interesting.

---

## Comment 5

> Username: anarthal  
> Created at: 2023-08-17 21:38:55 UTC  
> Url: https://github.com/boostorg/cobalt/issues/76#issuecomment-1683014652  

2. seems to not cover default-constructible types, either, unless I'm missing something.  
  
I'd go for `promise<result<T>> read(std::nothrow)` or similar.

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2023-08-18 04:06:05 UTC  
> Url: https://github.com/boostorg/cobalt/issues/76#issuecomment-1683320755  

Is this really a general issue or rather one concerning using channels in a `select` statement?

---

## Comment 7

> Username: anarthal  
> Created at: 2023-08-18 09:34:30 UTC  
> Url: https://github.com/boostorg/cobalt/issues/76#issuecomment-1683638685  

In my case it was just with channels + select. I don't know enough about the library to say if this could happen under more situations.
