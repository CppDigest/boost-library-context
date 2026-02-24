# #149 - A typo in README? Undefined variable “dt” [Closed]

> Username: LionNatsu  
> Created at: 2024-01-02 08:27:44 UTC  
> Updated at: 2024-03-29 01:29:46 UTC  
> Closed at: 2024-03-29 01:29:46 UTC  
> Url: https://github.com/boostorg/cobalt/issues/149  

I found many `dt.expires_after` in README.md, for example:  
  
```c++  
// a single threaded main running on an io_context  
cobalt::main co_main(int argc, char ** argv)  
{  
    // wrapper around asio::steady_timer  
    asio::steady_timer tim{co_await cobalt::this_coro::executor};  
    dt.expires_after(std::chrono::milliseconds(100));  
  
    co_await tim.async_wait(cobalt::use_op);  
    co_return 0;  
}  
```  
  
Does the `dt` really mean `tim` or certain global variable defined elsewhere?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-01-09 13:04:40 UTC  
> Url: https://github.com/boostorg/cobalt/issues/149#issuecomment-1883029859  

Should be tim. Thanks for reporting!
