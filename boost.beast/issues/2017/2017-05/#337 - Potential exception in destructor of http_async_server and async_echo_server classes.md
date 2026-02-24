# #337 - Potential exception in destructor of http_async_server and async_echo_server classes [Closed]

> Username: vkrivopalov  
> Created at: 2017-05-03 23:07:13 UTC  
> Updated at: 2017-05-05 05:16:12 UTC  
> Closed at: 2017-05-05 04:55:10 UTC  
> Url: https://github.com/boostorg/beast/issues/337  

Hello,  
  
My friend came across the pattern that exists in the following classes:  
   examples/http_async_server.hpp  
    test/websocket/websocket_async_echo_server.hpp  
  
Both repeatedly call join() on std::thread-s from a vector thread_ like this:  
  
    ~http_async_server()  
    {  
        error_code ec;  
        ios_.dispatch(  
            [&]{ acceptor_.close(ec); });  
        for(auto& t : thread_)  
            t.join();  
    }  
  
This is a potential issue as std::thread::join() can throw an exception:  
http://en.cppreference.com/w/cpp/thread/thread/join   
and since C++11 all the destructors are implicitly noexcept which means std::terminate will be called should an exception occur.  
  
While both these classes are not part of the library core, it seems this code should be improved to provide better samples as most of the library users will rely on the provided code snippets while writing their own applications.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-04 02:58:39 UTC  
> Updated at: 2017-05-04 02:59:21 UTC  
> Url: https://github.com/boostorg/beast/issues/337#issuecomment-299087030  

Thank you for your interest in Beast! However, I believe that the current implementation is correct. Here are the conditions under which `std::thread::join` can generate an error:  
  
1. this->get_id() == std::this_thread::get_id() (deadlock detected)  
2. the thread is not valid  
3. invalid_argument if joinable is false  
  
None of these scenarios are applicable to the code in question. If it makes you feel better, you can add `assert(t.joinable())` before the call to `join` to make it clear that the author of the code understands what they are doing.  
  
If you feel that I am in error, please provide the scenario under which you think that the line `t.join()` will throw an exception?

---

## Comment 2

> Username: vkrivopalov  
> Created at: 2017-05-05 04:55:10 UTC  
> Url: https://github.com/boostorg/beast/issues/337#issuecomment-299375282  

Hi Vinnie,  
  
Thanks for the explanation! That makes sense. Please disregard this issue.  
And thanks for your library, it is really nice!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-05-05 05:16:12 UTC  
> Url: https://github.com/boostorg/beast/issues/337#issuecomment-299376923  

I appreciate the kind words!
