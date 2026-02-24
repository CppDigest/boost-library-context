# #139 - Moved generators never yield values [Closed]

> Username: daumantas-kavolis-sensmetry  
> Created at: 2023-11-13 14:28:57 UTC  
> Updated at: 2023-11-14 07:54:55 UTC  
> Closed at: 2023-11-14 07:54:47 UTC  
> Url: https://github.com/boostorg/cobalt/issues/139  

Example:  
  
```cpp  
auto gen() -> boost::cobalt::generator<int> {  
  co_yield 42;  
}  
  
auto consume(boost::cobalt::generator<int> gen) -> boost::cobalt::promise<void> {  
  int value = co_await gen;  
  //          ^^^^^^^^^^^^ never resumes  
  if (value != 42) {  
    std::terminate();  
  }  
}  
  
auto co_main([[maybe_unused]] int argc, [[maybe_unused]] char *argv[]) -> boost::cobalt::main {  
  auto g = gen();  
  co_await consume(std::move(g));  
}  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-11-14 07:54:55 UTC  
> Url: https://github.com/boostorg/cobalt/issues/139#issuecomment-1809699974  

Thanks for reporting. I'll see that I'll get the fix in before the release.
