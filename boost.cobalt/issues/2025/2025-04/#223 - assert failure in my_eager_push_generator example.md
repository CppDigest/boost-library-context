# #223 - assert failure in my_eager_push_generator example [Closed]

> Username: libbooze  
> Created at: 2025-04-07 15:52:01 UTC  
> Updated at: 2025-06-20 14:50:37 UTC  
> Closed at: 2025-06-20 14:50:37 UTC  
> Url: https://github.com/boostorg/cobalt/issues/223  

I was watching some cobalt youtube video that was going over documentation and I was trying around stuff that was being shown and there is an issue with the documentation.  
  
This example:  
  
```  
cobalt::generator<double, int> my_eager_push_generator(int value)  
{  
   while (value != 0)  
       value = co_yield value * 0.1;  
   co_return std::numeric_limits<double>::quiet_NaN();  
}  
  
cobalt::main co_main(int argc, char * argv[])  
{  
    // create the generator  
    auto g = my_generator(5);  
  
    assert(0.5 == co_await g(4)); // result of 5  
    assert(0.4 == co_await g(3)); // result of 4  
    assert(0.3 == co_await g(2)); // result of 3  
    assert(0.2 == co_await g(1)); // result of 2  
    assert(0.1 == co_await g(0)); // result of 1  
  
    // we let the coroutine go out of scope while suspended  
    // no need for another co_await of `g`  
  
    co_return 0;  
}  
```  
First issue is that `my_generator` is probably c/p from previous examples, but even with that fixed assert fails because of the FP imprecision.   
  
Issue can be seen here where I have replaced `assert` with `println`  
  
https://godbolt.org/z/Msxqxv5EG

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-04-08 11:04:03 UTC  
> Updated at: 2025-04-08 11:06:37 UTC  
> Url: https://github.com/boostorg/cobalt/issues/223#issuecomment-2786063001  

Thanks for reporting, I'll update that example in the next release.

---

## Comment 2

> Username: libbooze  
> Created at: 2025-04-11 12:39:21 UTC  
> Url: https://github.com/boostorg/cobalt/issues/223#issuecomment-2796802733  

> Thanks for reporting, I'll update that example in the next release.  
  
Thank you.   
Maybe also mention that `co_yield` return value is how `x` gets updated in the example. I think both me and video author got a bit confused about that. It is a basic mistake, but I guess documentation should be beginner friendly as much as reasonably possible.
