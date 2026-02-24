# #366 - Crash when calling interrupt() on a boost::thread, which has another boost::thread [Closed]

> Username: elsamuko  
> Created at: 2022-03-15 12:52:56 UTC  
> Updated at: 2022-05-13 22:52:59 UTC  
> Closed at: 2022-03-16 13:15:38 UTC  
> Url: https://github.com/boostorg/thread/issues/366  

Hi there,  
  
I'm getting a crash when calling `interrupt()` on an outer `boost::thread`, which runs an inner `boost::thread`, which is connected to a `thread_guard`. It's not crashing when calling `join()` manually on the inner thread.  
  
Crash:    
`terminate called after throwing an instance of 'boost::thread_interrupted'`  
  
Source:    
https://gist.github.com/elsamuko/6e178c37fa2cf8742cb6bf512f2ff866  
  
Compile & Run:    
http://coliru.stacked-crooked.com/a/46c512bf9a385fff  
  
I'm running on Ubuntu 18.04. with g++ 7.5.0 and got the latest boost 1.78.0.  
  
Let me know, if you need more information.  
Regards,  
Samuel

---

## Comment 1

> Username: sehe  
> Created at: 2022-03-15 17:36:04 UTC  
> Updated at: 2022-03-15 17:38:36 UTC  
> Url: https://github.com/boostorg/thread/issues/366#issuecomment-1068262773  

From my answer [here](https://stackoverflow.com/a/71486609/85371):  
  
You're mixing std::thread and boost::thread.  
  
Only boost::thread knows about interruption points. Use that to fix:  
  
**[Live On Coliru](http://coliru.stacked-crooked.com/a/813c04aeb1f5ddf7)**  
  
```c++  
    #include <iostream>  
    #include <thread>  
      
    #include <boost/thread.hpp>  
    #include <boost/thread/thread_guard.hpp>  
      
    void double_interrupt() {  
        boost::thread outer([] {  
            boost::thread inner([] {  
                while (true) {  
                    boost::this_thread::sleep_for(boost::chrono::milliseconds(1));  
                }  
            });  
            {  
                boost::this_thread::sleep_for(boost::chrono::milliseconds(1));  
                std::cout << "Interrupting inner" << std::endl;  
                boost::thread_guard<boost::join_if_joinable> guard(inner);  
            }  
        });  
        std::cout << "Interrupting outer" << std::endl;  
        outer.interrupt();  
        outer.join();  
    }  
    int main() {  
        std::cout << "Start" << std::endl;  
        double_interrupt();  
        std::cout << "End" << std::endl;  
    }  
```  
Prints  
  
    Start  
    Interrupting outer  
    End

---

## Comment 2

> Username: elsamuko  
> Created at: 2022-03-15 17:59:31 UTC  
> Updated at: 2022-03-15 17:59:43 UTC  
> Url: https://github.com/boostorg/thread/issues/366#issuecomment-1068289042  

@sehe `boost::sleep()` is an [interruption_point](https://www.boost.org/doc/libs/1_78_0/doc/html/thread/thread_management.html#thread.thread_management.tutorial.interruption), which I don't want in the inner loop. Imagine it as some work is done here.  
  
If you remove the sleep, it crashes again.

---

## Comment 3

> Username: sehe  
> Created at: 2022-03-15 19:57:22 UTC  
> Url: https://github.com/boostorg/thread/issues/366#issuecomment-1068409813  

I'll continue the interaction at https://stackoverflow.com/a/71486609/85371 to prevent duplication.

---

## Comment 4

> Username: elsamuko  
> Created at: 2022-03-16 13:15:38 UTC  
> Url: https://github.com/boostorg/thread/issues/366#issuecomment-1069116552  

I got a solution. It doesn't crash when using the `thread_guard` with a custom `struct non_interruptable_interrupt_and_join_if_joinable`:  
```cpp  
    // helper struct to interrupt a boost::thread within a boost::thread  
    struct non_interruptable_interrupt_and_join_if_joinable {  
        template <class Thread>  
        void operator()(Thread& t) {  
            if(t.joinable()) {  
                boost::this_thread::disable_interruption di;  
                t.interrupt();  
                t.join();  
            }  
        }  
    };  
```

---

## Comment 5

> Username: pdimov  
> Created at: 2022-05-13 22:52:59 UTC  
> Url: https://github.com/boostorg/thread/issues/366#issuecomment-1126561969  

Should be fixed with https://github.com/boostorg/thread/commit/f71e0f1645413c3291e37d766e5b04bc57c6e132 and https://github.com/boostorg/thread/commit/8db325363b8e91de23c062ec8964bb605ad89f11.
