# #203 - Request, create empty promise object that can be set later outside of a coroutine. [Open]

> Username: sandercm  
> Created at: 2024-09-23 09:37:59 UTC  
> Updated at: 2024-09-24 13:06:30 UTC  
> Url: https://github.com/boostorg/cobalt/issues/203  

Hi,  
  
I was wondering if it would be possible to add a method that would set a coroutine object from the "outside".  
For example   
```  
boost::cobalt::promise<std::string> request;  
// save the promise somewhere in a data struct  
// ....  
// recieve some network data  
// match it with a request  
request.set(data);  
```  
this would then resume any coroutine that is currently co_awaited on this promise. I found a way around this by using async channels but was wondering if it would be at all possible to do this without them?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-09-24 13:06:28 UTC  
> Url: https://github.com/boostorg/cobalt/issues/203#issuecomment-2371222014  

It is not, that's what channels are for.
