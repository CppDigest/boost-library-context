# #294 - child::wait_for crashing in global object destruction after call to ::exit() on Darwin [Closed]

> Username: uptycs-rmack  
> Created at: 2023-01-09 15:54:00 UTC  
> Updated at: 2023-01-09 16:00:47 UTC  
> Closed at: 2023-01-09 15:58:04 UTC  
> Url: https://github.com/boostorg/process/issues/294  

Hi, I recently upgraded our application from boost 1.66 to 1.81. On Darwin we began to encounter crashes in boost::child::wait_for. It appears that the new Darwin implementation that forks then exits is not compatible with our usage of boost::asio. The call to exit is triggering C++ global object destruction while boost::asio fibers are accessing them. It's only that signal catching process that's crashing which doesn't really break our application but it does result in diagnostic logs being collected. For the time being I've rolled back to the 1.66 spinning implementation of wait_for which resolves the issue. Happy to work with you to debug if you need additional information.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-01-09 15:56:38 UTC  
> Url: https://github.com/boostorg/process/issues/294#issuecomment-1375849597  

The `wait_for` functions are broken, I should've marked them as deprecated long ago. They just shouldn't exist as there's not reliable API. My `v2` doesn't have them either, and relies on asio if you want timed waits.  
  
You should be able to do the spinning solution manually by just using grabbing the `id` instead.

---

## Comment 2

> Username: uptycs-rmack  
> Created at: 2023-01-09 15:58:04 UTC  
> Url: https://github.com/boostorg/process/issues/294#issuecomment-1375851534  

Heh, ok, makes sense. I appreciate the fast response. -Ryan
