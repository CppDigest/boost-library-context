# #33 - Creating a coroutine2 in catch statement results in abort() if an exception is raised. [Closed]

> Username: pandersb  
> Created at: 2020-08-27 14:22:48 UTC  
> Updated at: 2020-08-29 15:34:21 UTC  
> Closed at: 2020-08-29 13:16:01 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/33  

We're currently bumping boost from 1.55 to 1.72, replacing coroutine for coroutine2 as a result.  
Our code uses coroutines extensively and after the bump some of our code and unit tests are no longer working.  
  
- Using Visual Studio 2019 16.7.2  
- Boost 1.72, compiled using    
    `b2.exe link=shared threading=multi runtime-link=shared extern-c-nothrow=off  address-model=64`  
- Project compiled with  
-- /EHs  
-- No Whole program optimization  
-- Optimization disabled (/Od)  
-- Target: x64  
  
Here is a gtest test case reproducing the problem.  
  
```c++  
TEST(coroutine, boost_test_coroutine2_exceptions) {  
  using CoroType = boost::coroutines2::coroutine<void>;  
  using Caller = CoroType::push_type;  
  using Coro = CoroType::pull_type;  
  
  Coro coro_throw_within_catch(  
    [&](Caller& c) {  
      // Resume execution in main  
      c();  
  
      // 1. Throw an exception  
      // 2. Catch it  
      // 3. In catch handler, start a new coro  
      // 4. throw an exception  
      //   
      // Expected behavior when coro() is called:  
      //    A std::exception is thrown  
      // What I see:  
      //    Program ends in abort() since it finds no exception handler  
  
      try {  
        throw std::exception("Bam!");  
      }  
      catch (std::exception&) {  
        Coro empty_coro([](Caller&) {});  
      }  
      throw std::exception("Boom");  
    });  
  
  EXPECT_THROW(coro_throw_within_catch(), std::exception);  
}  
```  
  
If the `Coro empty_coro([](Caller&) {});` is moved two lines down (out of the catch statement), there is no crash.

---

## Comment 1

> Username: pandersb  
> Created at: 2020-08-27 22:21:32 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/33#issuecomment-682220243  

Possibly related to https://github.com/boostorg/context/issues/125 which I think might have been closed prematurely.

---

## Comment 2

> Username: olk  
> Created at: 2020-08-29 13:16:01 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/33#issuecomment-683288852  

You must not call coroutines from within a catch block.

---

## Comment 3

> Username: pandersb  
> Created at: 2020-08-29 14:00:04 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/33#issuecomment-683294319  

Is this mentioned in the documentation?

---

## Comment 4

> Username: olk  
> Created at: 2020-08-29 15:11:34 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/33#issuecomment-683303164  

https://www.boost.org/doc/libs/1_74_0/libs/coroutine2/doc/html/coroutine2/coroutine/asymmetric.html

---

## Comment 5

> Username: pandersb  
> Created at: 2020-08-29 15:34:21 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/33#issuecomment-683305966  

Is it this sentence your're referring to?  
  
> (!) IMPORTANT  
> Do not jump from inside a catch block and than re-throw the exception in another execution context.  
  
I interpreted "re-throw the exception" as calling `throw` without arguments to re-throw the same exception.  
I also wasn't sure what _jump_ meant in this context.  
  
Your answer to this bug report was far more clear and I suggest the documentation is updated with that sentence:  
```  
(!) IMPORTANT  
You must not call coroutines from within a catch block.  
```
