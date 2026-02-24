# #1091 - websocket advanced servers, incorrect handling of timeouts [Closed]

> Username: Xecutor  
> Created at: 2018-04-04 10:46:09 UTC  
> Updated at: 2018-10-07 18:28:17 UTC  
> Closed at: 2018-10-07 18:28:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1091  

I'm toying with boost::beast examples provided with version 144 and server_advanced is crashing when an attempt to call control callback is made. I investigated the issue and tracked it down to the control_callback method. I find it's signature and/or implementation to be very strange. It is expecting that passed argument will live on. Example code in the advanced_server passes a temporal to this function and it shouldn't compile at all. And it does not by g++. But MSVC somehow compiles it, but then it is crashing in a call attempt. Here is a distilled version of what's going on:  
  
    #include <stdio.h>  
    #include <functional>  
  
    struct A{  
      std::function<void()> f;  
      template <class OP>  
      void assign(OP& op)  
      {  
        f=std::ref(op);  
      }  
      void make()  
      {  
        assign(std::bind(&A::foo, this));  
      }  
      void foo()  
      {  
        printf("foo\n");  
      }  
      void call()  
      {  
        f();  
      }  
    };  
  
    int main()  
    {  
      A a;  
      a.make();  
      a.call();  
    }  
  
and it indeed crashes when compiled by MSVC. If a normal reference is replaced with rvalue reference it can be compiled by g++, but still crashes. Why std::ref is used? It will take the address of a reference store it... Why not std::move()  or rvalue reference and simple assignment?  
  
Also, there is one place in this example where bind is used with this, instead of shared_from_this().

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-04-04 14:41:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1091#issuecomment-378624382  

This was an oversight, it has been fixed in Boost 1.66 and also in 1.67 the control callback is copied and is no longer passed by non-const ref. See: https://github.com/boostorg/beast/issues/1086

---

## Comment 2

> Username: Xecutor  
> Created at: 2018-04-05 02:56:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1091#issuecomment-378806159  

Oh. Great. Thanks. Actually, I installed Boost 1.66 with vcpkg, and it's not fixed there...  
  
Also, advanced flex server have some issue with wss. https session touches stream_ after it was moved from.  
  
Great library, but it takes some time to wrap one's mind around it.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-04-13 02:32:54 UTC  
> Updated at: 2018-04-13 02:37:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1091#issuecomment-381002921  

>https session touches stream_ after it was moved from  
  
Can you please give me more details about this? I have inspected the code and I don't see where it is happening.

---

## Comment 4

> Username: Xecutor  
> Created at: 2018-04-13 04:47:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1091#issuecomment-381022495  

After ssl_http_session::release_stream() is called there is a call to ssl_http_session::do_eof().

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-04-13 11:56:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1091#issuecomment-381112977  

Ah! The timer on the HTTPS session is not canceled on a WebSocket upgrade!

---

## Comment 6

> Username: karelvorechovsky  
> Created at: 2018-08-15 11:05:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1091#issuecomment-413165419  

Hello,  
shoudldn't the do_timeout() in ssl_websocket_session close or cancel the underlying socket in case the async_shutdown() times out?  
  
I am having difficulties to figure out how does the async_shutdown ever finish if the other peer disconnects during that operation.  
  
`ws_.lowest_layer().close();`

---

## Comment 7

> Username: JarleStrand  
> Created at: 2018-08-20 16:50:27 UTC  
> Updated at: 2018-09-02 11:47:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1091#issuecomment-414385909  

Could this be a solution?  
  
In class http_session:  
  
In function on_read, if upgrade, set timer expiry to  a "special value" (min):  
        // See if it is a WebSocket Upgrade  
        if(websocket::is_upgrade(req_))  
        {  
            timer_.expires_at((std::chrono::steady_clock::time_point::min)());  
  
  
and in function, on timer, detect the upgrade by the "special value":  
    {  
        if(ec && ec != boost::asio::error::operation_aborted)  
            return fail(ec, "timer");  
  
       // detect upgrade here   
        if(timer_.expires_at() == (std::chrono::steady_clock::time_point::min)())   
            return;   
         
  
(this "special value" approach was suggested here for cancelling timers: https://stackoverflow.com/questions/43168199/cancelling-boost-asio-deadline-timer-safely/43169596#43169596)  
  
I have made a pull request for the solution (#1240)
