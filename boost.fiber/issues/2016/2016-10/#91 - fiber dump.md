# #91 - fiber dump? [Closed]

> Username: Memoryss  
> Created at: 2016-10-27 15:25:20 UTC  
> Updated at: 2016-11-27 10:45:50 UTC  
> Closed at: 2016-11-27 10:45:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/91  

when I create a main fiber and join(), the program dump. why????

---

## Comment 1

> Username: olk  
> Created at: 2016-11-27 10:45:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/91#issuecomment-263115059  

You can not create a main fiber - the main fiber is simply the stack that the OS has created (-> main() or thread entry). So it would not make sense to join a main fiber (e.g. this would mean you wait till the application/thread termiantes).
