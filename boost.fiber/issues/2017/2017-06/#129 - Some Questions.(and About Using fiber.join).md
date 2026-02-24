# #129 - Some Questions.(and About Using fiber.join) [Closed]

> Username: sxysxy  
> Created at: 2017-06-04 13:34:56 UTC  
> Updated at: 2017-06-04 17:16:19 UTC  
> Closed at: 2017-06-04 17:16:19 UTC  
> Url: https://github.com/boostorg/fiber/issues/129  

I use this boost-fiber in my game engine, and get these questions:  
1. Is it highly real-time?   
2. A problem... I uses fiber.join() as fiber.resume in ruby(enter the fiber and continue), uses boost::this_fiber::yield() as Fiber.yield in ruby(return to father fiber), and get wrong result, then I write a piece of code:  
```cpp  
int main(){  
    boost::fibers::fiber f([&] {  
        while (true) {  
            puts("ppp");  
            boost::this_fiber::yield();  
        }  
    });  
    while (true) {  
        f.join()  
        puts("qqq");  
    }  
    return 0;  
}  
```  
  
As what I thinked, it should print as:  
```  
ppp  
qqq  
ppp  
qqq  
.......  
```  
  
But in fact it print:  
```  
ppp  
ppp  
ppp  
ppp  
....  
```  
It seems like boost::this_fiber::yield() does not return where the fiber was called.    
What should I do? Thanks a lot, I am tired of 6 hours working on config boost and using fibers..

---

## Comment 1

> Username: olk  
> Created at: 2017-06-04 17:14:32 UTC  
> Updated at: 2017-06-04 17:16:08 UTC  
> Url: https://github.com/boostorg/fiber/issues/129#issuecomment-306053415  

1.) no  
2. ) API of boost.fiber is equivalent to std::thread  
- you can only join a fiber once (your example is wrong)  
- this_fiber::yield() does yield to another fiber, which fiber will be resumed next depends on the scheduling algorithm (round-robin etc.; it is a customization point) - it does not necessarily resume the father fiber. For instance if you use a scheduler that respects fiber priority, the next fiber that gets resumed is the one with the highest priority in the ready-queue.  
  
I suggest to study the documentation of boost.fiber (contains examples) + the examples section.
