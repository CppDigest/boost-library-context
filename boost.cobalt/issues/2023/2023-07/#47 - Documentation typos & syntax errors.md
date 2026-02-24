# #47 - Documentation typos & syntax errors [Closed]

> Username: anarthal  
> Created at: 2023-07-24 11:12:56 UTC  
> Updated at: 2023-07-25 11:01:49 UTC  
> Closed at: 2023-07-25 11:01:49 UTC  
> Url: https://github.com/boostorg/cobalt/issues/47  

** Location: https://klemens.dev/async/#design:concepts**  
  
> This library exposed a set of   
  
This library exposes ...  
  
** Location: all document **  
  
> the `select` [verb]  
  
Should be "the select function [verb]"  
  
** Location: https://klemens.dev/async/#select **  
  
> The select function can be used to co_await one [Awaitable](https://klemens.dev/async/#awaitable)s  
  
The select function can be used to co_await one [Awaitable](https://klemens.dev/async/#awaitable)  
  
** Location: https://klemens.dev/async/#design:select **  
  
> The select must however wait for all awaitable to complete once it initiates the awaiting operation.  
  
This is confusing, since in the line just before, it's stated that  
  
> It awaits multiple [awaitable](https://klemens.dev/async/#awaitable)s in a pseudo-random order and will return the result of the first one to complete.  
  
If I've understood it correctly, I'd suggest something like:  
  
"`select` waits for any of the passed-in awaitables to complete, and returns a variant holding the alternative corresponding to the completed variant. It then interrupts the other awaitables using the interrupt_wait mechanism (link)."  
  
** Location: https://klemens.dev/async/#left_select **  
  
> left_select gets evaluated strictly from left to right, i.e. the left-most awaitable that is ready will be used. This can lead to starvation which is why [async/select.hpp](https://klemens.dev/async/#select) should be considered as a sound default.  
  
Should state that this consideration only applies when more than one awaitable is already ready.  
  
** Location: https://klemens.dev/async/#gather, https://klemens.dev/async/#join **  
  
> The gather function can be used to co_await multiple [Awaitable](https://klemens.dev/async/#awaitable)s at once with properly connected cancellations.  
  
What does "properly connected cancellations" mean? Does this get guaranteed for all async types? If that's the case, move this statement below and specify that this consideration applies only if you're writing your own awaitables.  
  
** Location: https://klemens.dev/async/#promise **  
  
> That is, it cannot be resumed.  
  
Suggestion: That is, it cannot `co_yield` and be resumed later.  
  
** Location: https://klemens.dev/async/#promise **  
  
> By using ` the task gets detached. Without the ` it would generate a nodiscard warning.  
  
Unrendered text regarding `+`  
  
** Location: https://klemens.dev/async/#promise **  
  
The concept "attached/detached" is not explained anywhere. By reading the code, looks like "an attached promise gets cancelled when destroyed, while a detached promise is not".  
  
** Location: https://klemens.dev/async/#promise **  
  
`  bool attached();` missing const specifier  
  
  
** Location: https://klemens.dev/async/#promise **  
  
> This allows to spawn promised with a simple +, e.g. +my_task().  
  
This allows to spawn a detached promise using ` +my_task()`.  
  
  
** Location: https://klemens.dev/async/#promise **  
  
> This allows code like while (p) co_await p:  
  
C++ syntax error.  
  
** Location: https://klemens.dev/async/#promise **  
  
> The thread promise has the following properties.  
  
You haven't explained what a "thread promise" is.  
  
** Location: https://klemens.dev/async/#generator **  
  
> A generator is an eager coroutine that can not only be resumed but yield values to the caller.  
  
A generator is an eager coroutine that can be resumed and yield values to the caller.  
  
** Location: https://klemens.dev/async/#generator **  
  
> Values can be pushed into the generator, when Push is set to non-void:  
  
The declaration of generator is not there, so no context about Push is present. Suggestion:  
  
"Values can be pushed into the generator by specifying a second, non-void template argument to `generator`."  
  
** Location: https://klemens.dev/async/#generator **  
  
> This will cause the generator to post it’s own ...  
  
Should be "its"  
  
** Location: https://klemens.dev/async/#generator **  
  
>  auto operator()(      Push && push);  
  auto operator()(const Push &  push);  
  
Document what does this `auto` translate into.  
  
** Location: https://klemens.dev/async/#generator **  
  
> this is inefficient for synchronous generators.   
  
Document what is a "synchronous generator" (I thought they were all asynchronous)  
  
** Location: https://klemens.dev/async/#task **  
  
>   // enable `co_await` only for rvalues.  
  auto operator co_await () **;  
  
Should be &&  
  
  
** Location: https://klemens.dev/async/#detached **  
  
The type is a template in the docs but not in the actual code.  
  
** Location: https://klemens.dev/async/#detached **  
  
> async::main co_main(int argc, char *argv[])  
{  
  delay(std::chrono::milliseconds(50));  
  co_return 0;  
}  
  
Seems to not use the function that's being developed (`delayed_print`)  
  
  
** Location: https://klemens.dev/async/#detached **  
  
> This allows code like while (p) co_await p:  
  
C++ syntax error  
  
** Location: https://klemens.dev/async/#detached **  
  
>   // enable `co_await`.   
  auto operator co_await ();  
  
Extra backticks  
  
** Location: https://klemens.dev/async/#async_operation **  
  
`async_operation` doesn't exist in code  
  
** Location: https://klemens.dev/async/#channel **  
  
>  // read a value to a channel  
  __read_op__  read();  
  // write a value to the channel  
  __write_op__ write(const T  && value);  
  __write_op__ write(const T  &  value);  
  __write_op__ write(      T &&  value);  
  __write_op__ write(      T  &  value);  
  // write a value to the channel if T is void  
  __write_op__ write();  
  
Unrendered types  
  
** Location: https://klemens.dev/async/#channel **  
  
> Channels are a tool to have two coroutines communicate and synchronize  
  
Missing period.  
  
  
** Location: https://klemens.dev/async/#channel **  
  
> A channel type be void, in which case write takes no parameter.  
  
A channel type _can_ ...  
  
  
** Location: https://klemens.dev/async/#with **  
  
> async::promise<void> work(my_resource & res;  
  
C++ syntax error.  
  
  
** Location: https://klemens.dev/async/#with **  
  
> The teardown can either be done by providing an exit member function or a tag_invoke function that returns an [Awaitable](https://klemens.dev/async/#awaitable), .  
  
Extra trailing comma.  
  
** Location: https://klemens.dev/async/#with **  
  
Please document that the `std::exception_ptr` is `nullptr` if the scope exit happens without an active exception.
