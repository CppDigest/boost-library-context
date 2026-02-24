# #195 - Performance advice [Closed]

> Username: peroket  
> Created at: 2024-07-30 06:53:03 UTC  
> Updated at: 2025-07-04 10:50:17 UTC  
> Closed at: 2025-07-04 10:50:17 UTC  
> Url: https://github.com/boostorg/cobalt/issues/195  

Hello, first I want to say thank you for creating cobalt, it is exactly what I was looking for!  
  
I would like to pick your brain to find a solution to my problem, which is that I want to introduce coroutines in my code, but I want to introduce the asynchronicity gradually, meaning at the moment very few parts of my code can actually be interrupted.  
  
On top of that, I have a series of abstraction on top of the code that can actually interrupt, several layers of functions and classes until the very bottom. In my first try, I transformed everything that needed it to coroutines (so the ones that can actually interrupt and everything that calls it, which also has a different implementation of the same function through virtual that cannot interrupt, which changed almost every function), and it killed my performance, I guess because of creating so many coroutines that will anyway not interrupt.  
  
Is there a way to introduce coroutines at the lowest part of my code without impacting the performance so much when not interrupting?  
  
I saw for example that seastar has a "thread concept" with a future.get() method that will interrupt somehow without changing all the functions calling it, could I do something like that with cobalt?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-07-30 12:04:49 UTC  
> Url: https://github.com/boostorg/cobalt/issues/195#issuecomment-2258187754  

What do you mean by interrupted in this context?

---

## Comment 2

> Username: peroket  
> Created at: 2024-07-30 12:58:48 UTC  
> Url: https://github.com/boostorg/cobalt/issues/195#issuecomment-2258291552  

I mean my function put in the background so that the thread can continue to execute another one that is not waiting for a result.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-07-31 00:19:30 UTC  
> Url: https://github.com/boostorg/cobalt/issues/195#issuecomment-2259406101  

Ok, there are two options I think  
  
A. Run everything async on another thread (using cobalt::spawn or cobalt::thread) and just use regular locks between the async-thread and sync thread.  
  
B. Use the experimental (!) stackful coroutines [released in the next boost version] for implicit asynchronicity  (https://www.boost.org/doc/libs/master/libs/cobalt/doc/html/index.html#context) and put the `context` object into a `thread_local` global. I usually would not recommend this approach, but in your case it might be a good enough temporary solution.  
  
```cpp  
thread_local std::optional<experimental::context<task<void>>> stackful_root;  
  
template<typename T>  
auto await(T && value)  
{  
    assert(stackful_root);  
    return stackful_root->await(std::forward<T>(value));   
}  
  
  
cobalt::promise<void> your_new_async_code();  
void your_old_sync_code() {  
   await(your_new_async_code());     
}  
  
void root_func(experimental::context<task<void>> h)  
{  
   stackful_root = std::move(h);  
}  
  
  
int main()  
{  
   // create the stackful coroutine  
   cobalt::task<void> t = cobalt::experimental::make_context(&root_func);  
   cobalt::run(std::move(t));  
   return 0;  
}  
  
  
```

---

## Comment 4

> Username: peroket  
> Created at: 2024-07-31 06:21:16 UTC  
> Url: https://github.com/boostorg/cobalt/issues/195#issuecomment-2259758903  

Thanks I will try that
