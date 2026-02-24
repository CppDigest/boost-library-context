# #1629 - Custom data struct for each acceptor thread [Closed]

> Username: benstadin  
> Created at: 2019-06-03 17:02:19 UTC  
> Updated at: 2019-06-04 08:38:25 UTC  
> Closed at: 2019-06-04 08:38:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1629  

I'm experimenting with the async example and create an io_context with 2x number of CPU cores.   
  
For performance reasons within my own code, I want to keep some data for each of those threads (e.g. I thereby avoid to maintain an object pool which I require at the moment).   
  
Does beast offer an option to attach custom data to the acceptor threads?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-06-03 17:41:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1629#issuecomment-498355703  

No. I would use `thread_local`.
