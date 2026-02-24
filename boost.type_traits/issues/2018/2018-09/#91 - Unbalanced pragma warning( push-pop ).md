# #91 - Unbalanced pragma warning( push/pop ) [Closed]

> Username: PG-Devs  
> Created at: 2018-09-14 14:51:31 UTC  
> Updated at: 2018-09-17 09:03:01 UTC  
> Closed at: 2018-09-14 16:41:38 UTC  
> Url: https://github.com/boostorg/type_traits/issues/91  

In include/boost/type_traits/is_virtual_base_of.hpp  
  
  
![is_virtual_base_of patch](https://user-images.githubusercontent.com/22977594/45557659-66fadd80-b83e-11e8-8e1b-653db95830fe.gif)  
(rename to .patch)

---

## Comment 1

> Username: Olipro  
> Created at: 2018-09-14 15:12:33 UTC  
> Url: https://github.com/boostorg/type_traits/issues/91#issuecomment-421390372  

Why would you do this? Make a pull request, or if you don't know how to use git, put it in a pastebin (GitHub has one, there's various others). There are a myriad of options considerably less ridiculous than uploading a patch as a gif.

---

## Comment 2

> Username: PG-Devs  
> Created at: 2018-09-17 07:11:20 UTC  
> Updated at: 2018-09-17 09:03:01 UTC  
> Url: https://github.com/boostorg/type_traits/issues/91#issuecomment-421910536  

Thanks for committing this anyway. We spent a few hours figuring out the problem. When we finally had a patch, we found out that doesn't allow attaching a patch for some odd reason, but it does allow `.gif`. Creating a pull request seemed like a lot of overhead at this point, maybe next time.
