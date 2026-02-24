# #76 - Is it possible to support returning move-only types from signal? [Closed]

> Username: mocabe  
> Created at: 2024-04-28 08:41:25 UTC  
> Updated at: 2024-06-01 07:31:57 UTC  
> Closed at: 2024-05-31 13:55:39 UTC  
> Url: https://github.com/boostorg/signals2/issues/76  

As of Boost 1.84.0, returning move-only types from `boost::signals2::signal` fails to compile.   
  
```cpp  
boost::signals2::signal<std::unique_ptr<int>()> sig;  
sig(); //  error: overload resolution selected deleted operator '='  
```  
[Godbolt Link](https://godbolt.org/z/ax7xaEe1G)  
  
The error comes from use of [deprecated](https://github.com/boostorg/optional/blob/b7a1d666f1fd658ff2d758dffe6a4cb9d11e07c1/include/boost/optional/optional.hpp#L393) `boost::optional::reset`, which does not support move-only types.  
Replacing it with `operator=` seems work (with custom combiner), but I'm not sure if this is safe thing to do, hence the question.

---

## Comment 1

> Username: fmhess  
> Created at: 2024-04-29 16:16:52 UTC  
> Url: https://github.com/boostorg/signals2/issues/76#issuecomment-2083148314  

Are you talking about the reset call in detail/slot_call_iterator.hpp ?  That can be replaced with operator= but it isn't enough to make optional_last_value work.  I guess that's why you used a custom combiner?

---

## Comment 2

> Username: mocabe  
> Created at: 2024-04-30 00:40:05 UTC  
> Url: https://github.com/boostorg/signals2/issues/76#issuecomment-2083966456  

@fmhess Yes. For my use case I need custom combiner anyway, but making `optional_last_value`  work with move-only types would be nice too.

---

## Comment 3

> Username: fmhess  
> Created at: 2024-05-31 13:55:39 UTC  
> Url: https://github.com/boostorg/signals2/issues/76#issuecomment-2142229913  

optional_last_value and last_value can return move only types now, merged to master.

---

## Comment 4

> Username: mocabe  
> Created at: 2024-06-01 07:31:56 UTC  
> Url: https://github.com/boostorg/signals2/issues/76#issuecomment-2143342164  

@fmhess Thank you!
