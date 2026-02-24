# #147 - Remarks about the documentation [Closed]

> Username: blastrock  
> Created at: 2020-07-08 15:05:17 UTC  
> Updated at: 2021-07-13 19:03:10 UTC  
> Closed at: 2021-07-13 19:03:09 UTC  
> Url: https://github.com/boostorg/context/issues/147  

Hi,  
  
I have finished porting my code from execution_context to fiber and I have a few remarks about the documentation. I'm putting them all in this issue. Some remarks are about the call/cc API, but they apply also to the fiber API.  
  
- What's the difference between the fiber and call/cc APIs?  
  The only one I noticed is that the call/cc context function is called upon construction of the continuation object whereas the fiber is called on first resume. It would be nice to explain the differences somewhere, maybe in the Overview.  
  
- You say "Do not jump from inside a catch block and then re-throw the exception in another continuation.".  
  I'd just say "Do not jump from inside a catch block". Here's an example of a weird behavior that happens without rethrowing an exception:  
  
  ```cpp  
  {  
    namespace ctx = boost::context;  
  
    std::cout << (bool)std::current_exception() << std::endl;  
  
    ctx::continuation source = ctx::callcc([](ctx::continuation&& sink) {  
      try  
      {  
        throw 1;  
      }  
      catch (...)  
      {  
        std::move(sink).resume();  
      }  
      return std::move(sink);  
    });  
  
    std::cout << (bool)std::current_exception() << std::endl;  
  
    // output:  
    // 0  
    // 1  
  }  
  ```  
  `current_exception()` has returned an exception even though we are not in a catch block.  
  
  Note that the coroutine-TS forbids jumping from a catch block (this is checked at compilation time).  
  
- About `resume_with()`, you say that "The function passed as argument must accept a rvalue reference to continuation and return void.".  
  However the examples always return the continuation.  
  
- There is an unreachable line of code in the exception example:  
  ```cpp  
           throw my_exception(std::move(c),"abc");  
           return std::move( c);  
  ```  
  This looks like a bug because you move `c` twice, but it's actually ok because the return statement isn't reached.

---

## Comment 1

> Username: olk  
> Created at: 2021-06-01 15:38:34 UTC  
> Url: https://github.com/boostorg/context/issues/147#issuecomment-852227073  

ty

---

## Comment 2

> Username: olk  
> Created at: 2021-07-13 19:03:09 UTC  
> Url: https://github.com/boostorg/context/issues/147#issuecomment-879327856  

fiber_context API was preferred by the C++ standardization committee over call/cc API.  
  
other issues are fixed  
  
ty
