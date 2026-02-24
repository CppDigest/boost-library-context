# #26 - Problems with the receiver.cpp example. [Closed]

> Username: mBarkany  
> Created at: 2025-01-27 15:41:20 UTC  
> Updated at: 2025-03-25 12:26:43 UTC  
> Closed at: 2025-03-25 12:26:43 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/26  

Dear Mireo team!  
  
First of all: this async-mqtt5 library is a really professional work. Keep going!  
  
I recently tried to extend my personal project with a receiving capability and I realized that there are problems with this example:  
https://github.com/mireo/async-mqtt5/blob/v1.0.2/example/receiver.cpp  
  
1.  The "async_mqtt5::subscribe_props {}" at https://github.com/mireo/async-mqtt5/blob/v1.0.2/example/receiver.cpp#L43C27-L43C43 will be a nullptr and will cause an exception. Suggestion:  
  
> async_mqtt5::subscribe_props props = {};  
>   
> // Subscribe to a single Topic.  
>     auto &&[ec, sub_codes, sub_props] = co_await mqttClient->async_subscribe(  
>         this->topics_converted, props, use_nothrow_awaitable);  
  
2.  The client was not started so I got "Operation canceled" error.    
  
  
Thank you. :)

---

## Comment 1

> Username: ksimicevic  
> Created at: 2025-01-28 07:33:01 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/26#issuecomment-2618117035  

Hello!  
  
Thank you for the kind words and for reporting this issue!  
  
Are you using a GCC compiler by any chance?   
We have noticed that specific versions of the GCC compiler have an [internal bug](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=98401) where they do not correctly handle temporaries in `co_await` expressions. We detected that when compiling with g++ 11 and 12.  
  
Could this be causing your issue?

---

## Comment 2

> Username: mBarkany  
> Created at: 2025-01-28 13:57:19 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/26#issuecomment-2619080757  

I'm using 11.4.0. So yes. I will change to a newer version.  
  
Is there a list about the known issues by the way?  
  
Thank you!

---

## Comment 3

> Username: ksimicevic  
> Created at: 2025-01-29 11:27:48 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/26#issuecomment-2621383187  

Currently, this is the only compiler issue we are aware of. Our only answer to that issue is to do precisely what you have described and avoid using temporaries in `co_await` expressions.  
We will update our example code to reflect that. Sorry for the inconvenience!  
  
Our other issues are open here on our GitHub page.
