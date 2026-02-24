# #2 - How to terminate an uncompleted coro and unwind it's stack from outside? [Closed]

> Username: 0x1997  
> Created at: 2016-08-01 08:31:53 UTC  
> Updated at: 2021-07-16 01:55:23 UTC  
> Closed at: 2016-08-01 08:38:25 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/2  

like, force the coroutine function to raise a `forced_unwind`? or do I have to implement cancellation points manually?

---

## Comment 1

> Username: olk  
> Created at: 2016-08-01 08:38:25 UTC  
> Updated at: 2016-08-01 08:40:19 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/2#issuecomment-236521576  

docu:  
clean-up  
On coroutine destruction the associated stack will be unwound.  
The constructor of coroutine allows you to pass a customized stack-allocator. stack-allocator is free to deallocate the stack or cache it for future usage (for coroutines created later).   
  
http://www.boost.org/doc/libs/1_61_0/libs/coroutine2/doc/html/coroutine2/coroutine.html  
  
Simply, let the coroutine object go out of scope (call destructor).

---

## Comment 2

> Username: 0x1997  
> Created at: 2016-08-01 13:08:31 UTC  
> Updated at: 2016-08-01 13:08:59 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/2#issuecomment-236575486  

@olk Thanks. The problem is `delete coro` may raise `forced_wind` when segmented stacks are enabled.  
  
``` cpp  
#include <stdio.h>  
  
#include <boost/coroutine2/coroutine.hpp>  
  
using Coro = boost::coroutines2::coroutine<void>::push_type;  
  
int main()  
{  
    Coro* x;  
    Coro* y;  
  
    x = new Coro([&y](auto& yield) {  
        printf("x: new y\n");  
        y = new Coro([](auto& yield) {  
            printf("y: yield\n");  
            yield();  
            printf("y: return\n");  
        });  
  
        printf("x: yield\n");  
        yield();  
        printf("x: delete y\n");  
        delete y;  
        printf("x: return\n");  
    });  
  
    printf("enter x\n");  
    (*x)();  
    printf("enter y\n");  
    (*y)();  
    printf("enter x\n");  
    (*x)();  
    printf("delete x\n");  
    delete x;  
}  
```  
  
``` sh  
>> g++ -std=gnu++14 -DBOOST_USE_SEGMENTED_STACKS -fsplit-stack -pthread -lboost_context test.cc -o test && ./test  
enter x  
x: new y  
x: yield  
enter y  
y: yield  
enter x  
x: delete y  
delete x  
terminate called after throwing an instance of 'boost::coroutines2::detail::forced_unwind'  
[1]    16953 abort (core dumped)  ./test  
```  
  
However this works without segmented stacks.

---

## Comment 3

> Username: olk  
> Created at: 2016-08-01 15:33:29 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/2#issuecomment-236616422  

It was a bug in boost.coroutine2 (assignment of current execution_context (v1) was missing). Fixed in branch develop.

---

## Comment 4

> Username: 0x1997  
> Created at: 2016-08-01 15:53:55 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/2#issuecomment-236622526  

@olk thanks!
