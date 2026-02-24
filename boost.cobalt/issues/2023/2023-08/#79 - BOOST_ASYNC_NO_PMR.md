# #79 - BOOST_ASYNC_NO_PMR [Closed]

> Username: anarthal  
> Created at: 2023-08-16 18:51:07 UTC  
> Updated at: 2023-08-31 03:37:36 UTC  
> Closed at: 2023-08-31 03:37:36 UTC  
> Url: https://github.com/boostorg/cobalt/issues/79  

There seems to be no docs or testing regarding this macro.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-17 01:18:35 UTC  
> Url: https://github.com/boostorg/cobalt/issues/79#issuecomment-1681466978  

Yeah that shouldn't being develop or master. Shouldn't hurt anyone though.   
  
The idea is to allow disabling all the PMR stuff, but I didn't complete it yet.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-08-21 06:47:37 UTC  
> Updated at: 2023-08-22 03:19:38 UTC  
> Url: https://github.com/boostorg/cobalt/issues/79#issuecomment-1685747413  

I tried to, but the select/gather/join impl relies on pmr. So no pmr will be removed.
