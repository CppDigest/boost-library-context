# #108 - https://github.com/gnuradio/gnuradio/issues/2432 [Closed]

> Username: Red-Owl  
> Created at: 2019-04-03 14:21:57 UTC  
> Updated at: 2019-04-03 15:06:11 UTC  
> Closed at: 2019-04-03 15:06:10 UTC  
> Url: https://github.com/boostorg/filesystem/issues/108  



---

## Comment 1

> Username: Red-Owl  
> Created at: 2019-04-03 14:23:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/108#issuecomment-479511548  

build volk and use new version of boost 1.70.0 in debian 9 unstable   
old version not have this error

---

## Comment 2

> Username: Lastique  
> Created at: 2019-04-03 15:06:10 UTC  
> Url: https://github.com/boostorg/filesystem/issues/108#issuecomment-479529915  

Most likely, you're linking against a different version of Boost libraries. Check that library directories in your project include the directory with Boost 1.70 built libraries.  
  
Also, in the future please provide a problem description.
