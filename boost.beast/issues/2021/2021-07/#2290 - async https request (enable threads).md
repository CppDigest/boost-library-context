# #2290 - async https request (enable threads) [Closed]

> Username: paulnegz  
> Created at: 2021-07-15 03:44:38 UTC  
> Updated at: 2021-08-03 10:00:21 UTC  
> Closed at: 2021-08-03 10:00:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2290  

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
> Created at: 2021-07-19 18:48:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2290#issuecomment-882776623  

Has this program worked previously, or is this a new program that doesn’t work?
