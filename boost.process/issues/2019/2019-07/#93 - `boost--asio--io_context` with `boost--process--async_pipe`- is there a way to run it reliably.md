# #93 - `boost::asio::io_context` with `boost::process::async_pipe`: is there a way to run it reliably? [Open]

> Username: goodguysoft  
> Created at: 2019-07-11 13:29:30 UTC  
> Updated at: 2019-07-14 07:22:28 UTC  
> Url: https://github.com/boostorg/process/issues/93  

I published this question on SO, may be you have any ideas how to fix it:  
  
https://stackoverflow.com/questions/56989886/boostasioio-context-with-boostprocessasync-pipe-is-there-a-way-to-r

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2019-07-13 06:27:09 UTC  
> Url: https://github.com/boostorg/process/issues/93#issuecomment-511094091  

Why do you use a strand?

---

## Comment 2

> Username: goodguysoft  
> Created at: 2019-07-14 07:22:28 UTC  
> Url: https://github.com/boostorg/process/issues/93#issuecomment-511179914  

I experience this issue in much more complex application that my code sample. In this application I use one IO context to handle shared data accessed by deadline timer, TCP sockets and also command line tool. I use strand to avoid `mutex`, as far as I understood from `boost` documentation, it is better for performance. Anyway, I can switch code to mutex if `async_pipe` is incompatible with `strand`, however, I never saw anyway in documentation that I have to avoid `strand` if I use `async_pipe`.
