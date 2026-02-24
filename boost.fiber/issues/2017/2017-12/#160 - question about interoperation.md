# #160 - question about interoperation [Closed]

> Username: publicocean0  
> Created at: 2017-12-18 17:26:53 UTC  
> Updated at: 2017-12-23 10:21:36 UTC  
> Closed at: 2017-12-23 10:21:36 UTC  
> Url: https://github.com/boostorg/fiber/issues/160  

can i call a boost fiber from a JNI interface ?

---

## Comment 1

> Username: olk  
> Created at: 2017-12-18 18:17:54 UTC  
> Url: https://github.com/boostorg/fiber/issues/160#issuecomment-352513679  

I don't know how JNI interacts with teh C++ runtime ...  
I guess it should work but better you check it with a test app (would be nice if you would report your conclusion).

---

## Comment 2

> Username: publicocean0  
> Created at: 2017-12-23 08:48:54 UTC  
> Updated at: 2017-12-23 09:43:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/160#issuecomment-353714728  

ok i m writing a little library for porting your fibers nativelly using java, because i was using another lib  in c++ , in this way fiber is working in all the project (even if i dont understand because fiber was not placed in OS kernel replacing inside posix threads  fibers ).  
  
How can i stop(interrupt) a fiber using c++ boost?  
for example when this task continue to work in unexpected way   
`fiber.stop()`  
  
How can i execute fiber join(time) using c+ boost(the default api dont accept a maximun time for waiting) ?   
for example:  
  
` fiber.join(1000);`  
  
it is possible to serialize suspend  a fiber , serialize it in the disk and then to reattach in the thread?   
I m not sure but if want create a fibers pool i dont know if it ncessary to create also a thread pool over fibers. The cpu time is splitted in different threads , but if there is a unique thread all the time is executing inside there so theoretically to create a thread pool is not necessary, right?.

---

## Comment 3

> Username: olk  
> Created at: 2017-12-23 10:21:36 UTC  
> Url: https://github.com/boostorg/fiber/issues/160#issuecomment-353718544  

The boost.fiber-API is modelled after the std::thread-API, so you can use common patterns.  
If you can want to stop a fiber you need to suspend it (for instance via boost::fibers::condition_variable) or let it return from the function you have passed to fiber's ctor (that terminates the fiber).  
You can not serialized a fiber because this would not be require to copy the stack. At elast you would have to gauranttee the pointers to object (on stack or heap) etc. and additionally you could easily violate RAII ... so serializing would be a bad thing (ad is impossible without compielr support).  
Fibers run in a thread; a thread can handle 1000x fibers. You need only threads if you want to run fibers in parallel.
