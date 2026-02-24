# #100 - some documentation bugs/quesitons [Closed]

> Username: akrzemi1  
> Created at: 2023-09-02 06:04:56 UTC  
> Updated at: 2023-10-16 15:04:18 UTC  
> Closed at: 2023-10-16 15:04:18 UTC  
> Url: https://github.com/boostorg/cobalt/issues/100  

This is a feedback from the review of the documentaiotn.  
  
(1) . Section "Motivation" says:  
  
> It takes a collection of concepts from other languages and provides them based on  C++20 coroutines.  
>   
>   - easy asynchronous base functions, such as an async main & threads  
  
What does this "threads" mean here? A link in the docs would be useful.   
  
(2). Section "Coroutine Premier" > "Coroutines" gives example of a value consuming generator that does not compile:  
  
```  
async::generator<int, bool> iota()  
{  
  int i = 0;  
  bool more = false;  
  do  
  {  
    more = co_yield i++;  
  }  
  while(more);  
  co_return ;  
}  
```  
  
Apparently, one has to yield and return values of the same type. But a general problem is that this example is unrelaistic. If I no longer require new vales, I just abandon the coroutine (and trigger the call to `.destroy`. Why would I inform the coroutine body that I want to break the loop. And this brings me to an even more general concern. I have seen no realistic motivating example for a value-consuming generator. Nor can I imagine one. Is it possible that this feature has no application?  
  
(3). Section "Coroutine Premier" > "Awaitables":  
  
- [ ] "Awaitables are types that can be used in a `co_await` expression." -> "Awaitables are types that can be used as an operand in a `co_await` expression."  
- [ ] "In a `co_await` statement" -> "In a `co_await` expression"  
- [x] "The *return_type* is the result type of the coroutine" -> "The *return_type* is the type of the `co_await` expression"  
- [ ] Regarding the term "immediate completion", maybe you want to offer a section "Concepts" or "Definitions" that would list and describe the concepts used by this library. You could then make every ocurrence of "immediate completion" a link to the place where it is defined.  
  
(4). Regarding "async uses an `asio::io_context` as its default event loop.":  
  
- [ ] Could you make `asio::io_context` link to the corresponding seciton of Boost.Asio docs?  
- [ ] "default event loop" implies that this library can work on other event loops. Is this the case? If so, could the docs show an example of this, and link to it from where statement "async uses an `asio::io_context` as its default event loop" appears?

---

## Comment 1

> Username: akrzemi1  
> Created at: 2023-09-02 07:21:57 UTC  
> Updated at: 2023-09-02 07:25:25 UTC  
> Url: https://github.com/boostorg/cobalt/issues/100#issuecomment-1703747101  

(5). In section "Tutorial" > "Delay" we have a comment:  
  
> The [co_main](https://klemens.dev/async/#main) function defines an implicit `main` when defined and is the easiest way to enter asynchronous code.  
  
This is imprecise, and therefore confusing. Maybe you want to say:  
  
> (0) Header `<boost/async/main.hpp>` defines function `main` which performs setup required for aynchronous single-threaded computations, such as starting the default event loop and registering signal processing, and then calls `co_main` as an entry point to the application.  
  
Next, we have:  
> (2) Take the executor from the current coroutine promise.  
  
If this is an *immediate competion*, and I think it is, please indicate this.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2023-09-02 08:53:58 UTC  
> Url: https://github.com/boostorg/cobalt/issues/100#issuecomment-1703768895  

(6) The echo server tutorial has the following sentence in the description of the listener:  
  
> Since this is a tutorial, we’re using a [generator](https://klemens.dev/async/#generator).  
  
This seems to imply that a non-tutorial server implementation would not use `generator`. Is it true?

---

## Comment 3

> Username: akrzemi1  
> Created at: 2023-09-02 08:59:58 UTC  
> Updated at: 2023-09-02 09:00:11 UTC  
> Url: https://github.com/boostorg/cobalt/issues/100#issuecomment-1703769869  

(7). In the same echo server example:  
> This is a coroutine that can be co_awaited multiple times, until a `co_return` statement is reached.  
  
There is no `co_return` in the coroutine body. Maybe change it to something like:  
  
> This is a coroutine that can be `co_awaite`d multiple times, until the entire body of the coroutine has been executed.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2023-09-02 09:13:22 UTC  
> Url: https://github.com/boostorg/cobalt/issues/100#issuecomment-1703773452  

In the same echo server example we have:  
  
> The [wait_group](https://klemens.dev/async/#wait_group) is used to manage the running echo functions. This class will cancel & await the running echo coroutines.  
  
Do you mean, "This class will cancel & await the running echo coroutines when `wait_group` is destroyed"? Or that the interface of `wait_group` allows user to cancel & await the promises?  
  
Next sentence:  
  
> We do not need to do the same for the listener, because it will just stop on its own, when l gets destroyed. > The destructor of a generator will cancel it.  
  
I think that, unlike for the acceptor, the goal of the wait_group is to extend the lifetime of `async::promise` created my `echo()` in order not to incur an unnecessary synchronization upon each loop iteration. Am I right? If so, maybe the docs should state this explicitly. Otherwise it looks like saying that for `std::promise` we forgot to implement the stoppage of the coroutine in the destructor.   
  
Also "I gets" -> "it gets".

---

## Comment 5

> Username: akrzemi1  
> Created at: 2023-09-02 09:26:19 UTC  
> Url: https://github.com/boostorg/cobalt/issues/100#issuecomment-1703777606  

(9). use_op exmple has:  
  
> We can however implement our own ops, that can also utilize the async_ready optimization.  
  
Do you mean the "immediate completion"? If so, you should probably use this defined term.

---

## Comment 6

> Username: akrzemi1  
> Created at: 2023-09-02 17:04:53 UTC  
> Url: https://github.com/boostorg/cobalt/issues/100#issuecomment-1703889601  

(10). When describing the contents of `<async/this_thread.hpp>`, please wrap all the declarations into a namespace `this_thread`:  
  
```c++  
namespace async::this_thread {  
  pmr::memory_resource* get_default_resource() noexcept;   
  pmr::memory_resource* set_default_resource(pmr::memory_resource* r) noexcept;   
  pmr::polymorphic_allocator<void> get_allocator();   
  
  typename asio::io_context::executor_type & get_executor();   
  void set_executor(asio::io_context::executor_type exec) noexcept;   
}  
```  
  
Otherwise, there is no indication that they are in a dedicated namespace. It is the more difficult as we also have `std::this_thread` namespace.

---

## Comment 7

> Username: akrzemi1  
> Created at: 2023-09-02 17:19:01 UTC  
> Url: https://github.com/boostorg/cobalt/issues/100#issuecomment-1703893058  

(11). The documentation provides only section "Outline" for each header, but is missing the detailed description of individual functions. Because of this we do not know the contract of the functions: their preconditions, behavior in corner cases (such as empty vector passed to `select`), exception safety, and any other relevant information.
