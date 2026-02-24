# #252 - Cannot compile using Intel C++ Compiler [Closed]

> Username: vaskomitanov  
> Created at: 2017-02-05 04:31:14 UTC  
> Updated at: 2018-04-10 22:31:10 UTC  
> Closed at: 2017-02-18 12:01:41 UTC  
> Url: https://github.com/boostorg/beast/issues/252  

Using beast library and example from https://github.com/vinniefalco/Beast/tree/master.  
  
Using ICC: 17.0.1 20161005  
There are few different (COMPILER) bugs preventing Beast to compile.  
  
Is it possible to do some form of workaround to allow Intel C++ 2017 Update 1 to compile this great library?  
  
Regards,  
Vasko  
  
  
When compiling the example, compiler fails with "internal error":  
  
In file included from /home/vasko/xxx/ext/beast/include/beast/core/handler_concepts.hpp(11),  
  
                 from /home/vasko/xxx/ext/beast/include/beast/core/async_completion.hpp(11),  
                 from /home/vasko/xxx/ext/beast/include/beast/http/parse.hpp(12),  
                 from /home/vasko/xxx/ext/beast/include/beast/http.hpp(17),  
                 from /home/vasko/xxx/yyy/request_handler.hpp(6),  
                 from /home/vasko/xxx/yyy/server.hpp(1),  
                 from /home/vasko/xxx/yyy/main.cpp(1):  
/home/vasko/xxx/ext/beast/include/beast/core/detail/is_call_possible.hpp(41): internal error: assertion failed at: "shared/cfe/edgcpfe/scope_stk.c", line 11203  
  
      : decltype(is_call_possible_test<R>(

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-02-05 11:38:30 UTC  
> Url: https://github.com/boostorg/beast/issues/252#issuecomment-277514272  

I don't have the Intel C++ compiler, so it would be very difficult for me to make the necessary fixes and maintain Beast for Intel. Also, to my knowledge the Intel C++ compiler is not available on travis.org so there is no way to incorporate it into the continuous integration system. I have limited resources, so I'm not going to be able to do much about this in the short term. If Beast makes it into Boost, then it will be more practical since there might be other people with the compiler who could assist with keeping it working.

---

## Comment 2

> Username: vaskomitanov  
> Created at: 2017-02-08 08:10:26 UTC  
> Url: https://github.com/boostorg/beast/issues/252#issuecomment-278259137  

Fair enough answer.  
  
Is it possible at least to download the trial version of the compiler to take a look at least the issues or if that is too much work I understand.  
  
Anyway thank you Vinnie, excellent product.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-02-18 12:01:41 UTC  
> Url: https://github.com/boostorg/beast/issues/252#issuecomment-280841235  

I was not able to download the trial version due to licensing restrictions. I'm going to close this for now. After Beast goes through the Boost formal review, I may revisit it - thanks!

---

## Comment 4

> Username: ezzt  
> Created at: 2018-04-10 19:41:49 UTC  
> Url: https://github.com/boostorg/beast/issues/252#issuecomment-380223019  

Sorry to bring up this old issue ticket. Now that beast is merged into boost, is there any plan to support icc? Thanks.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-04-10 22:31:10 UTC  
> Url: https://github.com/boostorg/beast/issues/252#issuecomment-380268286  

I am more than happy to support systems which can fit into my workflow but Travis does not have support for Intel C++ compiler, and Intel puts a lot of obstacles in the way for someone to make their code compatible (without buying the product). I'm always happy to accept pull requests which fix specific issues.
