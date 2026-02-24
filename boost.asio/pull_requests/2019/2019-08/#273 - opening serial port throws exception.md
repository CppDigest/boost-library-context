# #273 opening serial port throws exception [Closed]

> Username: HosseinSagha  
> Created at: 2019-08-24 08:18:32 UTC  
> Updated at: 2020-11-05 05:01:56 UTC  
> Closed at: 2020-11-05 05:01:56 UTC  
> Url: https://github.com/boostorg/asio/pull/273  

@chriskohlhoff With the previous commit, SetCommState() returned ERROR_INVALID_PARAMETER which caused an exception to be thrown by open().  
dcb.BaudRate = 0 was not valid.

---

## Comment 1

> Username: HosseinSagha  
> Created_at: 2019-08-24 08:29:58 UTC  
> Url: https://github.com/boostorg/asio/pull/273#issuecomment-524532182  

Ah. Forgot to update the comment.

---

## Review 2 [Commented]

> Username: HosseinSagha  
> Created_at: 2019-11-13 23:10:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/asio/pull/273#pullrequestreview-316620776  

📁 include/boost/asio/detail/impl/win_iocp_serial_port_service.ipp

```diff
  94 |   dcb.fInX = FALSE;
  95 |-   dcb.fRtsControl = DTR_CONTROL_DISABLE;
  95 |+   dcb.fRtsControl = RTS_CONTROL_DISABLE;
```

> Username: HosseinSagha  
> Created_at: 2019-11-13 23:10:42 UTC  
> Updated_at: 2019-11-13 23:10:43 UTC  
> Url: https://github.com/boostorg/asio/pull/273#discussion_r346052152  

wrong macro was used.


---

## Comment 3

> Username: JamesOldfield  
> Created_at: 2019-12-20 11:29:25 UTC  
> Url: https://github.com/boostorg/asio/pull/273#issuecomment-567894073  

This pull request fixes the invalid default Baud rate by making an arbitrary specific choice of 9600. In my opinion, it would be better to just delete that line of code, which means the Baud rate is left at its current value retrieved from `GetCommState()` just above. Similarly, I suggest deleting the lines for the other options that you can set in ASIO: flow control, parity, stop bits, character size.  
  
My reasoning is that serial port options are preserved across closing and reopening the port, and even across different programs - you can set them on the command line with `mode` then open the port in a program and expect it to reflect your settings. This is useful functionality that used to exist in ASIO. Besides that use case, it's simply just confusing for the parameters to change without you asking for it.  
  
Actually I think it's very dubious that ASIO calls `SetCommState` at all, even for options you can't set explicitly in ASIO, but I don't have enough experience with serial ports to argue that forcefully. But I would argue that it should at least ignore any error code from `SetCommState` (perhaps log it if handler tracking is enabled): if a serial port driver doesn't support one of the options ASIO sets then I think it would be better to allow the port still be opened.

---

## Comment 4

> Username: Superlokkus  
> Created_at: 2020-01-15 10:43:25 UTC  
> Url: https://github.com/boostorg/asio/pull/273#issuecomment-574603016  

Another way would be to use the values one can give via `basic_serial_port::set_option`. I find it suspicious that one is expected to set these options only after opening the port, without clear documentation.

---
