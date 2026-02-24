# #182 - Custom logger: code clarification [Closed]

> Username: ropieur  
> Created at: 2022-05-02 09:45:33 UTC  
> Updated at: 2022-05-02 16:21:52 UTC  
> Closed at: 2022-05-02 11:22:53 UTC  
> Url: https://github.com/boostorg/log/issues/182  

Hello,  
  
I tried to implement a custom multithreaded logger by following [tutorial](http://www.boost.org/doc/libs/release/libs/log/doc/html/log/extension/sources.html).  
  
Could you provide a link to documentation or at least explain a bit more the next line?  
```  
    // Extract the named argument from the parameters pack  
    std::string tag_value = args[my_keywords::tag | std::string()];  
```  
Many thanks

---

## Comment 1

> Username: ropieur  
> Created at: 2022-05-02 11:22:46 UTC  
> Url: https://github.com/boostorg/log/issues/182#issuecomment-1114741042  

I found in [boost::parameter](http://www.boost.org/doc/libs/release/libs/parameter/) library

---

## Comment 2

> Username: Lastique  
> Created at: 2022-05-02 16:21:52 UTC  
> Url: https://github.com/boostorg/log/issues/182#issuecomment-1115088572  

Right, the syntax comes from [Boost.Parameter](https://www.boost.org/doc/libs/1_79_0/libs/parameter/doc/html/index.html#id36):  
  
- `args[keyword]` - extracts the parameter `keyword` value from the parameter pack `args`. Fails to compile if such parameter is not present in the pack.  
- `args[keyword | def]` - extracts the parameter `keyword` value from the parameter pack `args`. If no such parameter is in the pack, returns `def` value.  
- `args[keyword || fun]` - extracts the parameter `keyword` value from the parameter pack `args`. If no such parameter is in the pack, returns the value returned by `fun()` call.
