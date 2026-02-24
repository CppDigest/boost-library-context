# #184 - Compilation failure when enabling BOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT [Closed]

> Username: lakor64  
> Created at: 2023-11-23 17:10:27 UTC  
> Updated at: 2024-02-18 11:34:04 UTC  
> Closed at: 2024-02-18 11:34:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/184  

Hello, I've tried to compile the library with `-DBOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT`, I used this define to default the executor for simple interoperation with boost::asio::io_context.  
  
I've tried to make a simple program that includes boost/mysql.hpp and with this define enabled the program cannot build anymore.  
I'm not sure if this issue was already reported.  
  
Target Boost.mysql version: 1.83.0 (from vcpkg)  
Target Boost.asio version: 1.83.0 (from vcpkg)  
Target platform: MSVC/Visual Studio 2022 (c++17/c++20) SDK Windows 11

---

## Comment 1

> Username: avoyyit  
> Created at: 2023-11-23 17:11:35 UTC  
> Url: https://github.com/boostorg/mysql/issues/184#issuecomment-1824743292  

++ Same issue

---

## Comment 2

> Username: anarthal  
> Created at: 2023-11-23 19:25:57 UTC  
> Url: https://github.com/boostorg/mysql/issues/184#issuecomment-1824843144  

I hadn't heard of this option before. Reading the docs, I suspect this is related to the type-erasing options that were introduced in 1.83 (meaning `any_completion_handler` is incompatible with this option). I'm currently out, let me confirm this for you on Monday. Thanks for reporting.

---

## Comment 3

> Username: anarthal  
> Created at: 2023-11-27 12:53:13 UTC  
> Url: https://github.com/boostorg/mysql/issues/184#issuecomment-1827778707  

I can confirm this issue is related to what I described. I've opened https://github.com/chriskohlhoff/asio/issues/1386 in Asio to request this compatibility to be added.  
  
There's unfortunately no workaround for this at the moment.  
  
May I ask what's your reason to define `BOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT`? You can interoperate with `boost::asio::io_context` without defining it. You'd only need it if some part of your code (or another library) is using the legacy `executor` type. @lakor64 @avoyyit if you could please describe me your use case for defining the macro, it could help me guide my development.  
  
On my side, I can provide compatibility with `BOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT` but it's not a minor change, so the soonest I could release it would be Boost 1.85.  
  
Thanks,  
Ruben.

---

## Comment 4

> Username: lakor64  
> Created at: 2023-11-27 13:17:23 UTC  
> Url: https://github.com/boostorg/mysql/issues/184#issuecomment-1827816777  

Thank you for looking at this issue.  
I've tested that boost::asio::signal_set doesn't compile by default without that being added, I "fixed it" by defining basic_signal_set<io_context::executor_type>, similar declarations in sockets also had this issue apperently.  
On another note, my MySQL implementation and several other implementations are currently a big templates  
(eg. template <typename T> class MySQL) to avoid issues with the boost::asio::any_executor_type / boost::asio::io_context.  
This define can allow me to hold a ref to the io_context everywhere defeating the templates.  
I'm unsure if much of this template issues I have them because of broken implementations made by me or this new upgrades that happend. I could post more context in case

---

## Comment 5

> Username: anarthal  
> Created at: 2023-11-27 16:29:57 UTC  
> Url: https://github.com/boostorg/mysql/issues/184#issuecomment-1828181363  

Oh, that's weird. `signal_set` works for me with the default executor: https://godbolt.org/z/xPfYjxqxT (this is not defining `BOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT`).  
  
To provide some context on this executor stuff (apologies if you already know some of this):  
* TS executors are an old executor model. The idea was that executors were a lightweight handle to contexts (such as `asio::io_context` and `asio::thread_pool`) that had functions allowing dispatching callbacks to these contexts, such as `post` and `dispatch`. This was a relatively simple model and was proposed for standardization.  
* This didn't pass the standardization process, and a new, more extensible (and more complex) model was proposed, the "standard executors". These have properties that can be get and set. This hasn't worked out either, but is the model that is now preferred. However, the two models live together in Asio, to some extent.  
* You've got individual context executors, such as `io_context::executor_type`. These fulfill both models.  
* However, making everything a template caused trouble (code bloat, build times), and type-erased executors were created. The idea is similar to `std::function`: you can create a type-erased executor from anything that has the appropriate executor functions. In this field, you have `asio::executor`, which implements the 1st model, and `asio::any_executor`, which implements the second model.  
* In general, Asio considers the 1st model obsolete and defaults to the 2nd model. By default, `asio::any_io_executor` points to `asio::any_executor`. By defining `BOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT`, you make it point to the old `executor` class.  
  
So, if you don't template anything, all your executors will be `asio::any_io_executor`, which can be constructed from `io_context::executor_type`.  
  
You can actually retrieve a reference to the `io_context` from a `asio::any_io_executor` via the property system:  
  
```  
// This assumes you haven't defined BOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT and that ex always points to a io_context  
asio::io_context& get_context_ref(asio::any_io_executor ex)  
{  
    return static_cast<asio::io_context&>(asio::query(ex, asio::execution::context));  
}  
```  
  
Full code: https://godbolt.org/z/jbK94efvn  
  
If you can provide me more context, I may be able to help you more.  
  
Thanks,  
Ruben.

---

## Comment 6

> Username: anarthal  
> Created at: 2023-12-07 17:30:56 UTC  
> Url: https://github.com/boostorg/mysql/issues/184#issuecomment-1845787344  

I've seen that this has been fixed by Boost.Asio. It hasn't been released yet though. If it doesn't make 1.84, it will make 1.85. I'll keep you guys posted.

---

## Comment 7

> Username: anarthal  
> Created at: 2023-12-20 16:58:30 UTC  
> Url: https://github.com/boostorg/mysql/issues/184#issuecomment-1864823588  

I can confirm that Boost.MySQL 1.84 works with `BOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT`.  
  
I'll leave this issue open to implement a CI build to test this configuration.
