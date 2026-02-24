# #81 - Lazy generator [Closed]

> Username: klemens-morgenstern  
> Created at: 2023-08-18 03:46:35 UTC  
> Updated at: 2023-08-31 08:02:29 UTC  
> Closed at: 2023-08-31 08:02:29 UTC  
> Url: https://github.com/boostorg/cobalt/issues/81  

The generator should be able to be used lazily, the following looks about right:  
  
```cpp  
generator<int, double> gen()  
{  
   double x = co_await initial; // wait for the first co_await from the caller & make it lazy  
}  
```
