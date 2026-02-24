# #2685 - Internal latency measurements [Closed]

> Username: sfjhkjdsh  
> Created at: 2023-05-14 12:33:42 UTC  
> Updated at: 2024-01-04 09:32:11 UTC  
> Closed at: 2024-01-04 09:32:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2685  

My use case is to measure the latencies for reading WS msg: from network card / Kernel -> `stream::async_read` callback i.e. internal latency. This will allow me to track slow consumer (packet queueing inside kernel) and beast's latency.  
  
One approach can be: As per [kernel](https://www.kernel.org/doc/html/latest/networking/timestamping.html), `SO_TIMESTAMPING` can be used for per byte timestamps. So beast could report timestamps associated with the first and last byte in a WS message.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-05-15 00:51:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2685#issuecomment-1547053364  

Why can't you just timestamp the call & completion of `async_read` ?

---

## Comment 2

> Username: sfjhkjdsh  
> Created at: 2023-05-15 08:15:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2685#issuecomment-1547398418  

there can be times when there's no data on socket, so waiting time will be included as well in the measurement?
