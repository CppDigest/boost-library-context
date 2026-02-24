# #2289 - async https request (enabling threads) [Closed]

> Username: paulnegz  
> Created at: 2021-07-15 03:34:38 UTC  
> Updated at: 2021-12-06 01:51:15 UTC  
> Closed at: 2021-12-06 01:51:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2289  

OS: Ubuntu bionic  
```  
    resolver_.async_resolve(  
        host,  
        port,  
        beast::bind_front_handler(  
            &session::on_resolve,  
            shared_from_this()));  
}  
```  
using meson build system (https://mesonbuild.com/).  
After attaching the header files and libraries, I am unable to make a http request inside https://github.com/openbmc/phosphor-health-monitor/blob/master/healthMonitor.cpp   
![ERROR](https://user-images.githubusercontent.com/37233089/125712789-17cd4d71-0add-48e9-a4ea-1ec455e70226.JPG)  
  
  
  
Attached Dependencies:   
![mesonEdit](https://user-images.githubusercontent.com/37233089/125712258-6a48c0c4-7897-4c92-b392-a00dd8a30f17.JPG)  
  
  
### Version of Beast = 306

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-07-15 03:37:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2289#issuecomment-880366560  

What steps did you take to prepare your program for multiple threads?  
Are you using strands?

---

## Comment 2

> Username: paulnegz  
> Created at: 2021-07-15 03:44:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2289#issuecomment-880368567  

https://github.com/boostorg/beast/tree/develop/example/http/client/async-ssl example uses strands and that the sample code I followed but the error is about threads

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-07-15 03:46:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2289#issuecomment-880369247  

Are you saying that you started with our example and modified it to suit your needs, and it now does not function?  
  
or did you write a new program using our example as a tutorial?

---

## Comment 4

> Username: paulnegz  
> Created at: 2021-07-15 03:49:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2289#issuecomment-880370184  

I copied the example and tried to add it  to a .cpp file(https://github.com/openbmc/phosphor-health-monitor/blob/master/healthMonitor.cpp)

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-07-15 04:11:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2289#issuecomment-880376765  

Ok, probably a good idea to go back to the example, check it’s working, and then make incremental changes until it doesn’t.  
Whatever you did last is the problem.  
At the moment we don’t have a lot to go on.

---

## Comment 6

> Username: paulnegz  
> Created at: 2021-07-15 04:17:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2289#issuecomment-880378724  

That was my approach and the resolver_.async_resolve function throws an error

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-08-03 10:00:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2289#issuecomment-891711417  

Did you resolve this in the end?
