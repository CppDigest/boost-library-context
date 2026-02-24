# #11 - [Feature] Support for C++20 coroutines [Open]

> Username: Tradias  
> Created at: 2020-10-04 12:31:17 UTC  
> Updated at: 2024-04-02 20:36:59 UTC  
> Url: https://github.com/boostorg/leaf/issues/11  

To use this library with C++20 coroutine the call in `handle_error.hpp` - `try_handle_all` or `try_handle_some`:  
  
`if( auto r = std::forward<TryBlock>(try_block)() )`  
  
Should check whether the returned time is an Awaitable and use   
  
`if( auto r = co_await std::forward<TryBlock>(try_block)() )`  
  
instead. Similarly for the error handling functions. This would allow users to write code among the lines of:  
  
```c++  
boost:leaf::try_handle_some(  
    []() -> boost::asio::awaitable<boost:leaf::result<void>> {  
        co_return boost::leaf::result<void>{};  
    }, ...);  
```  
  
which is currently not possible. Maybe we could even achieve less verbosity. Ideally I would want to write `co_return {};` just like before, but I think ` boost::leaf::result<>` would need a constructor that takes an `initializer_list` then.

---

## Comment 1

> Username: zajo  
> Created at: 2020-10-04 18:18:44 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-703294766  

I'm not an expert on coroutines but this will likely not work in general. The error objects in LEAF are stored in a `leaf::context<>` in the scope of `try_handle_all`/`try_handle_some`/`try_catch`. Before calling the `TryBlock`, these functions first call `context<>::activate`, which binds `this` it to the calling thread (until `context<>::deactivate` is called). If the coroutine is executed in another thread, it will not be able to reach the `context<>` object and therefore it will not be able to transport any error objects.  
  
That said, I am exploring the possibility to support coroutines. At a minimum, this would require that upon suspend, all `context<>` objects in the calling thread are deactivated, then re-activated upon resume. As far as I can tell this should be possible. I'd welcome any help towards that goal.

---

## Comment 2

> Username: Tradias  
> Created at: 2020-10-04 18:56:40 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-703299807  

I would actually be fine with the usual single-threaded MVP first. Similar to how boost::leaf works in other places that do not use the `polymorphic_context`. I am not quite familiar with the internals of boost::leaf yet, but what you are describing sounds a bit like what I am be encountering in here: https://github.com/boostorg/leaf/issues/10

---

## Comment 3

> Username: zajo  
> Created at: 2020-10-04 20:18:15 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-703310244  

To support your use case, is it sufficient to `co_await` on the `try_block`? Would the static dispatch be based on the return type?

---

## Comment 4

> Username: Tradias  
> Created at: 2020-10-04 21:11:39 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-703316815  

Basically yes, it needs some better type deduction, but to give you an idea, this one compiles for me:  
  
handle_errors.hpp  
```c++  
	template <class TryBlock, class... H>  
	BOOST_LEAF_CONSTEXPR inline  
	boost::asio::awaitable<void>  
	try_handle_all( TryBlock && try_block, H && ... h ) noexcept  
	{  
		//static_assert(is_result_type<decltype(std::declval<TryBlock>()())>::value, "The return type of the try_block passed to a try_handle_all function must be registered with leaf::is_result_type");  
		context_type_from_handlers<H...> ctx;  
		auto active_context = activate_context(ctx);  
		if( auto r = co_await std::forward<TryBlock>(try_block)() )  
			co_return r.value();  
		else  
		{  
			error_id id = r.error();  
			ctx.deactivate();  
			//using R = typename std::decay<decltype(std::declval<TryBlock>()())>::type;  
			co_return ctx.template handle_error<void>(std::move(id), std::forward<H>(h)...);  
		}  
	}  
```  
  
```c++  
#include <boost/asio.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/leaf.hpp>  
  
namespace bl = boost::leaf;  
namespace asio = boost::asio;  
  
int main()  
{  
    asio::io_context io_context;  
    asio::co_spawn(  
        io_context,  
        []() noexcept -> boost::asio::awaitable<void> {  
            bl::try_handle_all(  
                []() noexcept -> boost::asio::awaitable<bl::result<void>> { co_return bl::result<void>{}; },  
                [](const bl::error_info&) {});  
            co_return ;  
        },  
        asio::detached);  
    io_context.run();  
}  
```

---

## Comment 5

> Username: zajo  
> Created at: 2020-10-18 05:42:46 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-711120509  

I think this will have to wait I need to do more research about coroutines before I commit to any support.

---

## Comment 6

> Username: ytimenkov  
> Created at: 2020-12-18 06:00:23 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-747887315  

I think this should be mentioned in the docs that the library has all this niceness like type erasure and attaching arbitrary data at cost of passing information out of band via global state .  
  
Just to make documentation less biased when comparing with outcome (as the latter can be used to pass information between threads conveniently without prior setup: since outcome's result is a value type it can be transferred from one thread to another when needed).  
  
With coroutines I think simply adding type are not enough because there can be multiple coroutines in suspended state each with own try blocks so context needs to be stored and restored together with coroutine suspension.  
Something like [AsyncLocal](https://docs.microsoft.com/en-us/dotnet/api/system.threading.asynclocal-1?view=net-5.0) comes to my mind...

---

## Comment 7

> Username: zajo  
> Created at: 2020-12-18 07:20:32 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-747917601  

To clarify, error objects in LEAF are not global; they use automatic storage duration in error-handling scopes (thread-local pointers are used to access the error objects).  
  
When necessary, error objects can also be stored in a `leaf::result`, you just need to collect them in a `leaf::context` first. This is automated using `make_shared_context`, see https://boostorg.github.io/leaf/#tutorial-async_result.  
  
As for coroutines, it would be nice to avoid the above, which is likely possible by deactivating/activating a `leaf::context` object when the coroutine suspends/resumes. I'm interested in developing this idea but at this point I don't understand coroutines enough to be able to implement it.

---

## Comment 8

> Username: ytimenkov  
> Created at: 2020-12-18 08:26:21 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-747943254  

@zajo First I think becoming a part of Boost is a big thing 👍  
  
Don't get me wrong: I didn't say that relying on globals is bad by definition (yes, I realize that leaf uses TLS but I consider it still global, and I wrote "information" not "error objects"). It's just a design choice, same as with logging: you may pass logger objects around with components and different levels "polluting" API or you may use globals to do this at the right place.  
  
Both have own strengths and weaknesses. And my point was to just present them so consumers could make a rational choice.  
I myself considering looking closer at it and maybe try using.  
  
With coroutines it may be reasonable to focus on asio first rather than making a generic coroutine-friendly wrappers.  
  
I agree that now there is not much information and all that generic code looks scary and tangled when trying to understand how things work and work together...  
  
This guide is worth reading, explains some details about handler <-> completion interaction (if you didn't see it): https://www.boost.org/doc/libs/1_75_0/libs/outcome/doc/html/recipes/asio-integration-1-70.html

---

## Comment 9

> Username: zajo  
> Created at: 2020-12-18 19:41:37 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-748284578  

[This section of the whitepaper](https://github.com/boostorg/leaf/blob/master/doc/whitepaper.md#5-error-handling-and-function-signatures) explains that designs that carry error objects in a `result<T,E>` couple function signatures with error types and why that is not a good idea. Let me know if you think this can be improved to help readers make a rational choice.  
  
The use of TLS is an implementation detail. It is incorrect for the documentation to specify implementation details, except as it pertains to portability.  
  
See [this example](https://github.com/boostorg/leaf/blob/master/examples/asio_beast_leaf_rpc.cpp) for using LEAF with ASIO.

---

## Comment 10

> Username: ProfDiesel  
> Created at: 2021-03-24 20:00:48 UTC  
> Updated at: 2021-03-29 20:08:10 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-806147602  

~~POC with asio.  
An abuse of template specialization on the `executor` and of `await_transform()` to emulate a "coroutine local storage".  
Far from satisfactory but seems to work.  
[https://github.com/ProfDiesel/leaf/commit/ec445ed75618211962998e8e04646c7fae9dccb3](https://github.com/ProfDiesel/leaf/commit/ec445ed75618211962998e8e04646c7fae9dccb3)~~  
  
**Edit on 20210329**:   
Probably not the best way to do it. Relying on ``co_await``/``await_transform``, though aesthetically pleasing, needs the coroutine to start suspended then wait for its frame to be pumped by the ``awaitable_thread``. Hooking ``awaitable_handler``s and activating/deactivating the context around the call to ``pump`` may be a way.  
  
  
Adapting the code from #10:  
```C++  
#include <asio.hpp>  
#include <boost/leaf.hpp>  
#include <iostream>  
  
#if defined(BOOST_NO_EXCEPTIONS)  
namespace boost  
{  
 void throw_exception(const std::exception &exception) { std::abort(); }  
} // namespace boost  
  
#endif //  defined(BOOST_NO_EXCEPTIONS)  
  
#if defined(ASIO_NO_EXCEPTIONS)  
namespace asio::detail  
{  
template<typename exception_type>  
void throw_exception(const exception_type &exception)  
{  
  boost::throw_exception(exception);  
}  
} // namespace asio::detail  
#endif // defined(ASIO_NO_EXCEPTIONS)  
  
namespace bl = boost::leaf;  
  
int main()  
{  
  asio::io_context io_context;  
  asio::co_spawn(  
    io_context,  
    [&]() noexcept -> bl::awaitable<void> {  
      std::clog << "try 1\n";  
      co_await bl::co_try_handle_all(  
        [&]() noexcept -> bl::awaitable<bl::result<void>> {  
          asio::steady_timer timer(io_context);  
          timer.expires_after(std::chrono::milliseconds(500));  
          std::clog << "before wait 1\n";  
          co_await timer.async_wait(bl::use_awaitable);  
          std::clog << "after wait 1\n";  
          bl::result<void> result = co_await bl::co_new_error(42);  
          co_return result;  
        },  
        [](int i) { std::clog << "error 1 " << i << "\n"; }, [](const bl::error_info &ei, int i) { std::clog << "error 1 " << ei << " " << i << "\n"; },  
        [](const bl::error_info &ei) { std::clog << "error 1 " << ei << "\n"; });  
      std::clog << "return 1\n";  
      co_return;  
    },  
    asio::detached);  
  asio::co_spawn(  
    io_context,  
    [&]() noexcept -> bl::awaitable<void> {  
      std::clog << "try 2\n";  
      co_await bl::co_try_handle_all(  
        [&]() noexcept -> bl::awaitable<bl::result<void>> {  
          asio::steady_timer timer(io_context);  
          auto result = co_await bl::co_new_error(43);  
          timer.expires_after(std::chrono::seconds(1));  
          std::clog << "before wait 2\n";  
          co_await timer.async_wait(bl::use_awaitable);  
          std::clog << "after wait 2\n";  
          co_return result;  
        },  
        [](int i) { std::clog << "error 2 " << i << "\n"; }, [](const bl::error_info &ei, int i) { std::clog << "error 2 " << ei << " " << i << "\n"; },  
        [](const bl::error_info &ei) { std::clog << "error 2 " << ei << "\n"; });  
      std::clog << "return 2\n";  
      co_return;  
    },  
    asio::detached);  
  io_context.run();  
}  
```

---

## Comment 11

> Username: zajo  
> Created at: 2021-03-24 22:14:19 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-806219547  

Thank you! I'll take a closer look later, dealing with an emergency right now.

---

## Comment 12

> Username: ProfDiesel  
> Created at: 2021-03-29 20:11:53 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-809679571  

No hurry. See the edit above. I'm still scratching my head wondering how this all can eventually be done.

---

## Comment 13

> Username: ProfDiesel  
> Created at: 2021-05-25 20:05:56 UTC  
> Updated at: 2021-05-25 20:06:57 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-848223909  

I got some time to play a bit with leaf lately.  
https://github.com/ProfDiesel/leaf/commit/c43308400dc04b5f3f59514ae0e078dece316cec  
As proposed above, this version is using a ``shared_context`` in the ``awaitable_thread``, with a (quite intrusive) change in the ``context`` to enable many levels of stack to be moved in and out at once.

---

## Comment 14

> Username: zajo  
> Created at: 2021-05-25 21:28:02 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-848278575  

Thanks, I really appreciate this. I'll try to look at your work soon, but I'm swamped with other work lately. Perhaps we should connect on CPP slack to discuss, you can help me understand what you've done.

---

## Comment 15

> Username: computerquip-work  
> Created at: 2024-04-02 18:46:05 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-2032802045  

Did anything come of this? I don't see a way to automatically deduce the type for co_return and the error macros don't appear to work within a coroutine. Is there an easier way to handle this with coroutines?

---

## Comment 16

> Username: computerquip-work  
> Created at: 2024-04-02 20:36:58 UTC  
> Url: https://github.com/boostorg/leaf/issues/11#issuecomment-2033047451  

Searching around the boost repositories, I found Boost Cobalt which appears to provide some sort of adapter for Boost Leaf: https://live.boost.org/doc/libs/1_84_0/libs/cobalt/doc/html/index.html#leaf  
  
I haven't tried it yet but I'll slap it here just in case.
