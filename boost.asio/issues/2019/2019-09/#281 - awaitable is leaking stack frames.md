# #281 - awaitable is leaking stack frames [Closed]

> Username: cedral  
> Created at: 2019-09-10 17:34:21 UTC  
> Updated at: 2019-10-25 01:04:58 UTC  
> Closed at: 2019-10-25 01:04:57 UTC  
> Url: https://github.com/boostorg/asio/issues/281  

I have a web server in which awaitable is leaking frames in impl\use_awaitable.hpp from here.   
```  
  template <typename Initiation, typename... InitArgs>  
  static return_type initiate(Initiation initiation,  
      use_awaitable_t<Executor>, InitArgs... args)  
  {  
    co_await [&](auto* frame)  
      {  
        handler_type handler(frame->detach_thread());  
        std::move(initiation)(std::move(handler), std::move(args)...);  
        return static_cast<handler_type*>(nullptr);  
      };  
  
    for (;;) {} // Never reached.  
#if defined(_MSC_VER)  
    co_return dummy_return<typename return_type::value_type>();  
#endif // defined(_MSC_VER)  
  }  
```  
I am not able to parse what this is trying to do. I think it must be generating a promise from that lambda. A promise which never completes or the Never reached part would get reached. I would appreciate any pointers as to where this is going or where I should look for why those frames are leaking.

---

## Comment 1

> Username: jefftrull  
> Created at: 2019-10-16 16:30:09 UTC  
> Url: https://github.com/boostorg/asio/issues/281#issuecomment-542785278  

This is interesting. Do you have a MWE?

---

## Comment 2

> Username: cedral  
> Created at: 2019-10-17 00:21:17 UTC  
> Url: https://github.com/boostorg/asio/issues/281#issuecomment-542943563  

I tried to make one but it didn't reproduce the problem. I am taking another stab at it but it may be a few days.

---

## Comment 3

> Username: cedral  
> Created at: 2019-10-25 01:04:57 UTC  
> Url: https://github.com/boostorg/asio/issues/281#issuecomment-546158848  

It was a bug in the compiler. The work around for my case was here  
[code gen issue with co_await and ternary operator](https://developercommunity.visualstudio.com/content/problem/537422/code-gen-issue-with-co-await-and-ternary-operator.html)
