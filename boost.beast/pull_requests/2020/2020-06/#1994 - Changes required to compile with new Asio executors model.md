# #1994 Changes required to compile with new Asio executors model [Closed]

> Username: chriskohlhoff  
> Created at: 2020-06-23 01:53:42 UTC  
> Updated at: 2020-06-30 23:59:14 UTC  
> Closed at: 2020-06-30 16:12:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1994  

Please consider this pull request as informative with respect to the minimal changes required, rather than merging as is.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-23 02:00:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1994#pullrequestreview-435388211  

📁 include/boost/beast/core/detail/bind_default_executor.hpp

```diff
  71 |+     // without forwarding to a user's hook.
  72 |+ 
  73 |+ #if 0
```

> Username: vinniefalco  
> Created_at: 2020-06-23 02:00:12 UTC  
> Updated_at: 2020-06-24 22:37:23 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#discussion_r443918795  

Note that `bind_default_executor.hpp` is unused.... I don't remember writing it, or what it was for.


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-23 02:01:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1994#pullrequestreview-435388457  

📁 include/boost/beast/core/detail/bind_handler.hpp

```diff
 160 |         Function&& f, bind_wrapper* op)
 161 |     {
 162 |         using net::asio_handler_invoke;
```

> Username: vinniefalco  
> Created_at: 2020-06-23 02:01:02 UTC  
> Updated_at: 2020-06-24 22:37:23 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#discussion_r443919025  

nit: These should say `boost::asio::asio_handler_invoke` not `net::asio_handler_invoke`, because this hook is not part of the TS and never will be.

> Username: madmongo1  
> Created_at: 2020-06-24 13:38:46 UTC  
> Updated_at: 2020-06-25 09:22:09 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#discussion_r444899863  

check


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-23 02:03:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1994#pullrequestreview-435389386  

📁 test/beast/core/make_strand.cpp

```diff
  31 |-         net::executor ex(ioc.get_executor());
  31 |+         net::any_io_executor ex(ioc.get_executor());
  32 |         net::make_strand(ex);
```

> Username: vinniefalco  
> Created_at: 2020-06-23 02:03:54 UTC  
> Updated_at: 2020-06-24 22:37:23 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#discussion_r443919792  

make_strand.cpp is unnecessary, it used to test Beast's `make_strand` function which existed before it was added to Asio. No Beast release ever shipped a `make_strand` function though.

> Username: madmongo1  
> Created_at: 2020-06-26 12:26:02 UTC  
> Updated_at: 2020-06-26 12:26:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#discussion_r446151994  

would you like it removed?


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-23 02:05:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1994#pullrequestreview-435390025  

📁 test/doc/core_1_refresher.cpp

```diff
 290 | {
 291 |-     using type = boost::asio::executor;
 291 |+     using type = boost::asio::any_io_executor;
```

> Username: vinniefalco  
> Created_at: 2020-06-23 02:05:53 UTC  
> Updated_at: 2020-06-24 22:37:23 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#discussion_r443920322  

This should say `net::` not `boost::asio::`


---

## Comment 5

> Username: vinniefalco  
> Created_at: 2020-06-23 02:21:34 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-647866413  

@madmongo1 we need to also go through all the new asio commits one by one and determine if they motivate beast changes.

---

## Comment 6

> Username: chriskohlhoff  
> Created_at: 2020-06-23 03:31:07 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-647886488  

You might also consider adding support for using beast with `BOOST_ASIO_NO_TS_EXECUTORS` defined. This could serve as an additional piece of evidence that the new executor model is suited to real production uses. (N.B. This will presumably entail #ifdefs in order to retain compatibility with TS executors.)

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2020-06-23 04:23:50 UTC  
> Updated_at: 2020-06-23 04:24:15 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-647898613  

Is `BOOST_ASIO_NO_TS_EXECUTORS` documented? Boost master branch closes for significant changes on June 24th by the way.

---

## Comment 8

> Username: madmongo1  
> Created_at: 2020-06-23 05:37:46 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-647919804  

Thanks very much for this. I’m guessing this needs to go into the 1.74 release?

---

## Comment 9

> Username: chriskohlhoff  
> Created_at: 2020-06-23 06:42:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-647942493  

> Is `BOOST_ASIO_NO_TS_EXECUTORS` documented?  
  
It will be. In a nutshell:  
  
* No suppport for TS executor type requirements (e.g. no member `context`, `dispatch`, `post`, `defer` in things like `io_context::executor_type`; no `asio::executor`; and no `executor_work_guard`).  
* Track outstanding work using a property: https://github.com/chriskohlhoff/asio/commit/165ea38ac82f2fafabac3882315053ecf4b89eca  
* Use a property to access an executor's execution context: https://github.com/chriskohlhoff/asio/commit/390673c322c1144b039f883d30c9b817bd0a9351  
  
> Boost master branch closes for significant changes on June 24th by the way.  
  
That's for breaking changes. [1 July](https://www.boost.org/development/index.html) for other changes. Still some more new things to come (e.g. the rest of the P0443 interface, time permitting).  
  
> I’m guessing this needs to go into the 1.74 release?  
  
Yep.

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2020-06-23 15:24:16 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-648235695  

> No suppport for TS executor type requirements  
  
I don't understand. Why would someone want to DISABLE support for TS features? Are these features going to stay in the TS or are they being removed?

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2020-06-23 15:25:25 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-648236422  

You mentioned these two features:  
  
* Track outstanding work using a property: chriskohlhoff/asio@165ea38  
* Use a property to access an executor's execution context: chriskohlhoff/asio@390673c  
  
Are these features disabled when defining BOOST_ASIO_NO_TS_EXECUTORS  or are they only available when defining BOOST_ASIO_NO_TS_EXECUTORS ? Are these property-based features going to be in the new TS or are you going in your own direction?

---

## Comment 12

> Username: chriskohlhoff  
> Created_at: 2020-06-24 00:53:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-648518710  

> > No suppport for TS executor type requirements  
>   
> I don't understand. Why would someone want to DISABLE support for TS features? Are these features going to stay in the TS or are they being removed?  
  
There's only one networking TS, which was the ISO document published in 2018. This #define disables the executor model (a.k.a. "TS executors") that was specified in that document, and only leaves the new proposed standard P0443 executors implementation enabled. Even if a new revision of the TS published, it is not expected to contain its own executors, thus "TS executors" refers only to that particular executor model.  
  
> You mentioned these two features:  
>   
> * Track outstanding work using a property: [chriskohlhoff/asio@165ea38](https://github.com/chriskohlhoff/asio/commit/165ea38)  
> * Use a property to access an executor's execution context: [chriskohlhoff/asio@390673c](https://github.com/chriskohlhoff/asio/commit/390673c)  
>   
> Are these features disabled when defining BOOST_ASIO_NO_TS_EXECUTORS or are they only available when defining BOOST_ASIO_NO_TS_EXECUTORS ? Are these property-based features going to be in the new TS or are you going in your own direction?  
  
Sorry I wasn't clear. These commits are to show the kinds of changes required to compile against an Asio library that is configured without networking TS executors, and only with P0443 executors. The changes here follow the model I outlined in P0958, and this implementation experience will inform an update to P0958.

---

## Comment 13

> Username: vinniefalco  
> Created_at: 2020-06-24 01:11:20 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-648523567  

It sounds like making this work is as "easy" as defining `BOOST_ASIO_NO_TS_EXECUTORS` in one of the Beast's test matrix entries, and then ensuring the result compiles :)

---

## Comment 14

> Username: madmongo1  
> Created_at: 2020-06-24 16:21:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-648922424  

> It sounds like making this work is as "easy" as defining `BOOST_ASIO_NO_TS_EXECUTORS` in one of the Beast's test matrix entries, and then ensuring the result compiles :)  
  
It doesn't.  
  
@chriskohlhoff I'm going to need some guidance. We have a custom executor called `test_executor` which encapsulates net.ts style io_context executor.  
  
In the non-net.ts world it's going to have to also expose (I presume?) a properties interface in order to function.  
  
Is there a guide somewhere on which properties need to be implemented and how to do it?

---

## Comment 15

> Username: chriskohlhoff  
> Created_at: 2020-06-24 22:33:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-649107978  

> Is there a guide somewhere on which properties need to be implemented and how to do it?  
  
Updated bind_handler test as an example.

---

## Comment 16

> Username: madmongo1  
> Created_at: 2020-06-25 07:18:53 UTC  
> Updated_at: 2020-06-25 07:34:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-649298696  

Hi @chriskohlhoff, thanks for the explanatory commit and narrative.  
  
I have another (hopefully simpler) question:  
  
in `async_base.hpp` we have a class called `async_base<Handler, Executor1>` who's executor_type is the result of `associated_executor_t<Handler, Executor1>`.  
  
We then store a work guard (now an any_io_executor) for the deduced executor.  
  
The get_executor() function used to be written as:  
  
```  
    executor_type  
    get_executor() const noexcept  
    {  
        return net::get_associated_executor(  
            h_, wg1_.get_executor());    // extract the Executor1 from the work_guard  
    }  
```  
  
In the NO_TS world, naively rewrtiting as:  
  
```  
        return net::get_associated_executor(  
            h_, executor_type(net::query(wg1_, net::execution::context)));  // wg1_ is an any_io_executor  
```  
  
But there is a problem here. If executor_type is a strand<>, then this will presumably construct a new strand rather than returning the old one?  
  
I'm thinking that rather than storing an any_io_executor, am I not better off storing the specific type of executor returned from   
```  
require(get_associated_executor(handler, ex), execution::outstanding_work.tracked);  
```   
?  
  
Can I therefore then guarantee that the I can convert this back into executor_type with:  
  
```  
require(wg1_, execution::outstanding_work.untracked);  
```  
  
ALthough it occurs to me that if I am passed a executor to a strand that tracks outstanding work as the default executor, then I'd have to remember that it has tracking enabled and then not apply the untracked transform in the call to get_executor().  
  
Am I overthinking this? Is it simply that work tracking sentinels have lost their ability to yield the original executor, and I'll have to store the executor separately?  
  
```  
ctor(Handler h, Executor1 dflt)  
: exec_copy_(get_associated_executor(h, dflt))   // make a copy of the actual executor  
, wg1_(prefer(exec_copy_), execution.tracked)   // make a transformed copy purely for tracking work  
, handler_(std::move(h))  
```  
  
or am I better off transforming the handler?  
  
```  
ctor(Handler h, Executor1 dflt)  
: handler_(bind_executor(get_associated_executor(h, dflt), std::move(h))) // transform the handler to associate with deduced work  
, wg1_(prefer(handler_.get_executor()), execution.tracked)   // make a transformed copy purely for tracking work  
```  
  
then get_executor() in both cases becomes:  
```  
executor_type  
get_executor()  
{  
  return handler_.get_executor();  
}  
```  
  
(I probably need another tracker to track the handler's original executor in case it's not on the same context as the default executor's context)  
  
I'm looking for guidance on best practice while seeking to avoid redundant copies.  
  
This idiom of using a work_guard as a convenient space-saving store of an executor is fairly common.  
  
Would it be a reasonable idea for beast to maintain it's own work_guard class to replace the one now missing in asio, or am I again thinking about this wrongly?

---

## Comment 17

> Username: chriskohlhoff  
> Created_at: 2020-06-25 07:47:29 UTC  
> Updated_at: 2020-06-25 07:48:24 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-649326353  

I'm wondering if this is an XY problem, i.e.:  
  
> in async_base.hpp we have a class called async_base<Handler, Executor1> who's executor_type is the result of associated_executor_t<Handler, Executor1>.  
  
As it's a composed operation, why not say that its executor_type is `decltype(asio::prefer(std::declval<const Executor1&>(), asio::execution::outstanding_work.tracked))`.  
  
One of the goals with this approach to work counting is that the conversion from an untracked to tracked work executor only occurs at the outermost layer of a stack of composed operations. Every layer lower down just gets an identity result from `asio::prefer(ex, asio::execution::outstanding_work.tracked)`, because at that point `ex` already tracks work.  
  
EDIT: should include associated_executor_t in there, but hopefully you get the idea.

---

## Comment 18

> Username: vinniefalco  
> Created_at: 2020-06-25 07:57:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-649336191  

Why isn't `get_executor` const? The implication here is that it can't be called concurrently.

---

## Comment 19

> Username: vinniefalco  
> Created_at: 2020-06-25 07:58:53 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-649337833  

Truth be told I am completely lost with understanding how these executors work now.

---

## Review 20 [Commented]

> Username: madmongo1  
> Created_at: 2020-06-25 09:22:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1994#pullrequestreview-436654957  

Thanks for the response Chris. A couple more questions as I wiggle down the rabbit hole:  
  
> I'm wondering if this is an XY problem, i.e.:  
  
No doubt  
  
> As it's a composed operation, why not say that its executor_type is `decltype(asio::prefer(std::declval<const Executor1&>(), asio::execution::outstanding_work.tracked))`.  
  
OK, that's sort of reasonable, it does mean of course that every sub-operation is going to modify the work reference counter, but I suppose that's no worse than before.  
  
Does `decltype(asio::prefer(std::declval<const Executor1&>(), asio::execution::outstanding_work.tracked))` have a name, or are we committed to decltype? I am seeking to avoid the polymorphic executor (as I actually do in recent asio programs destined for the real world), because the implied branch mispredictions in polymoprhism and redundant conditional branches work against high performance programs.  
  
> One of the goals with this approach to work counting is that the conversion from an untracked to tracked work executor only occurs at the outermost layer of a stack of composed operations. Every layer lower down just gets an identity result from `asio::prefer(ex, asio::execution::outstanding_work.tracked)`, because at that point `ex` already tracks work.  
  
I see, so it's expected that every copy of a work-tracking executor will still perform work reference counting.  
  
Presumably this means that when a handler is comitted to an execution context via post(), it must be implicitly associated with a new executor as if by:   
```  
auto new_ex = asio::prefer(handler.get_executor(), asio::execution::outstanding_work.tracked);  
context.execute(asio::bind_executor(std::move(new_ex), std::move(handler));  
```   
?

---

## Comment 21

> Username: madmongo1  
> Created_at: 2020-06-25 09:44:57 UTC  
> Updated_at: 2020-06-25 09:47:23 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-649431124  

> As it's a composed operation, why not say that its executor_type is `decltype(asio::prefer(std::declval<const Executor1&>(), asio::execution::outstanding_work.tracked))`.  
  
Wait, I don't think this is correct.   
  
There are two cases:  
  
1. supplied handler has a different executor to the default executor  
2. supplied handler has the same executor as the default handler  
  
In case 1, I need to track work on the default handler as well has the associated handler.  
  
In case 2, I only need to track work on the one handler.  
  
Bear in mind that it's quite possible that work is submitted in one execution context with a handler that will complete on a completely separate execution context... Or is this an unsupported use case?   
  
e.g.  
  
```  
asio::io_context iocs[2];  
  
auto t = io_thing(iocs[0].get_executor());  
t.async_something(asio::bind_executor(iocs[1].get_executor(), [](error_code) {});  
  
auto f = std::async(std::launch::async, [&] { iocs[1].run(); });  
iocs[0].run();  
f.wait();  
```  
In this case I would (currently) expect iocs[0] not to stop until the async_something request has completed.  
  
Am I wrong about this?

---

## Comment 22

> Username: chriskohlhoff  
> Created_at: 2020-06-25 10:21:09 UTC  
> Updated_at: 2020-06-25 10:25:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-649450848  

> > As it's a composed operation, why not say that its executor_type is `decltype(asio::prefer(std::declval<const Executor1&>(), asio::execution::outstanding_work.tracked))`.  
>   
> Wait, I don't think this is correct.  
  
Yeah, sorry, I was about to comment that I referred to the wrong thing here.  
  
This `prefer` isn't for `executor_type` directly. Instead, it relates to the "default candidate executor" (a.k.a. currently `Executor1`, a.k.a the "I/O executor") that you will pass in to `get_associated_executor`. I.e. don't store and pass in an `Executor1` object, instead store and pass in the result of the `prefer()`. In pseudocode:  
  
```cpp  
template <typename Handler, typename Executor1>  
struct async_base  
{  
  Handler handler_ = ...;  
  auto io_executor_ = asio::prefer(executor1, execution::outstanding_work.tracked);  
  
  auto get_executor() const noexcept  
  {  
    return get_associated_executor(handler_, io_executor_);  
  }  
  
  // ...  
};  
```  
  
> There are two cases:  
>   
> 1. supplied handler has a different executor to the default executor  
> 2. supplied handler has the same executor as the default handler  
>   
> In case 1, I need to track work on the default handler as well has the associated handler.  
>   
> In case 2, I only need to track work on the one handler.  
>   
> Bear in mind that it's quite possible that work is submitted in one execution context with a handler that will complete on a completely separate execution context... Or is this an unsupported use case?  
>   
> e.g.  
>   
> ```  
> auto asio::io_context iocs[2];  
>   
> auto t = io_thing(iocs[0].get_executor());  
> t.async_something(asio::bind_executor(iocs[1].get_executor(), [](error_code) {});  
>   
> auto f = std::async(std::launch::async, [&] { iocs[1].run(); });  
> iocs[0].run();  
> f.wait();  
> ```  
>   
> In this case I would (currently) expect iocs[0] not to stop until the async_something request has completed.  
>   
> Am I wrong about this?  
  
This should work automatically, as the composed operation implementation in `async_base` stores a copy of the I/O executor with `outstanding_work.tracked` applied.  
  
Following the above pseudocode, if case 1:  
* `async_base.io_executor_` tracks work against underlying I/O context  
* `async_base.get_executor()` returns handler-specific executor for other I/O context  
* The underlying async operation applies the `outstanding_work.tracked` property to the result of `async_base.get_executor()`.  
* Work is tracked against both executors, which refer to different I/O contexts  
  
If case 2:  
* `async_base.io_executor_` tracks work against underlying I/O context  
* `async_base.get_executor()` returns a copy of `async_base.io_executor_`  
* The underlying async operation applies outstanding_work.tracked property to result of `async_base.get_executor()`, but this is an identity transformation  
* Work is tracked against the I/O executor (twice)  
  
> Does decltype(asio::prefer(std::declval<const Executor1&>(), asio::execution::outstanding_work.tracked)) have a name, or are we committed to decltype?   
  
There's no name in the proposed standard executors, but in asio I have the `prefer_result_type` trait. (EDIT: whether you use this trait or `decltype`, make sure you `decay_t` the result that you end up storing, since the "expression-equivalent" nature of customisation points means you can get a forwarding reference as the return type.)  
  
> Presumably this means that when a handler is comitted to an execution context via post(), it must be implicitly associated with a new executor as if by:  
  
Yep, this is basically what `asio::detail::work_dispatcher` does.

---

## Comment 23

> Username: madmongo1  
> Created_at: 2020-06-25 10:32:31 UTC  
> Updated_at: 2020-06-25 10:35:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-649455985  

Thank your for your response.  
  
Another question:  
  
You mentioned earlier that the equivalent of `executor_work_guard::reset()` is the assignement of a default-constructed `any_io_executor`.   
  
a: Does this also hold for a `decltype(prefer(e, execution::outstanding_work.tracked))` ?  
  
b: if not, I take it that my equivalent of a net.ts `executor_work_guard::reset()` is a no-op, and I simply accept that work reference counts are decremented in a different order (upon final destruction of the composed operation)?  
  
  
here is a proposed solution to encapsulate the differences:  
  
https://gist.github.com/madmongo1/f4da59b89795b51f0c6e77e9596566ef

---

## Comment 24

> Username: chriskohlhoff  
> Created_at: 2020-06-25 11:21:42 UTC  
> Updated_at: 2020-06-25 11:27:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-649478826  

> You mentioned earlier that the equivalent of `executor_work_guard::reset()` is the assignement of a default-constructed `any_io_executor`.  
>   
> a: Does this also hold for a `decltype(prefer(e, execution::outstanding_work.tracked))` ?  
  
No, as generically executors are neither Assignable nor DefaultConstructible.  
  
> b: if not, I take it that my equivalent of a net.ts `executor_work_guard::reset()` is a no-op, and I simply accept that work reference counts are decremented in a different order (upon final destruction of the composed operation)?  
  
Yep, do this. I think technically the reset was never necessary. (EDIT: Necessary within a composed operation, that is.)  
  
That `any_io_executor` approach is more aimed at people who want to reset work at some high level point in the program e.g. in `main`.  
  
> here is a proposed solution to encapsulate the differences:  
>   
> https://gist.github.com/madmongo1/f4da59b89795b51f0c6e77e9596566ef  
  
Looks similar to what I did for `async_compose`:  
  
https://github.com/chriskohlhoff/asio/blob/master/asio/include/asio/impl/compose.hpp#L37  
  
but you need a `decay_t` on the `prefer` result type.

---

## Comment 25

> Username: madmongo1  
> Created_at: 2020-06-25 14:30:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-649584489  

> Looks similar to what I did for `async_compose`:  
>   
> https://github.com/chriskohlhoff/asio/blob/master/asio/include/asio/impl/compose.hpp#L37  
>   
> but you need a `decay_t` on the `prefer` result type.  
  
One day I'd like to buy you a beer and show you a my list of "things that should be in asio's public interface that aren't".   
  
:-)  
  
Many thanks for the pointers.

---

## Comment 26

> Username: madmongo1  
> Created_at: 2020-06-25 15:20:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-649617556  

Actually Chris, one more question.  
  
If I am writing a middleware component that needs to create a new strand from an existing polymorphic any_io_executor (or indeed aome type T that models an Executor) what is the correct way to do it?  
  
The previous `net::executor` didn't have an `is_strand()` member so you couldn't know (other than querying the type_id of the inner executor) whether it represented a strand or not. This made the default executor unsuitable for programs and libraries that could be configured to be multi-threaded or not (and indeed meant having to maintain a whole set of asio type aliases per project).  
  
If I used explicit executors I could do something equivalent to this:  
  
```  
#if PROGRAM_HAS_CONCURRENCY  
    template<class Executor>  
    auto new_strand(net::strand<Executor> const& e)  
    {  
        return net::make_strand(e.get_inner_executor());  
    }  
  
    template<class Executor>  
    auto new_strand(Executor const& e)  
    {  
        return net::make_strand(e);  
    }  
#else  
    template<class Executor>  
    auto new_strand(Executor const& e)  
    {  
        return e;  
    }  
#endif  
```  
  
In the new model, is this the same, or are there some properties I would manipulate to achieve the same thing?

---

## Comment 27

> Username: chriskohlhoff  
> Created_at: 2020-06-25 22:25:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-649848344  

> In the new model, is this the same, or are there some properties I would manipulate to achieve the same thing?  
  
```cpp  
#include <asio.hpp>  
#include <cassert>  
  
template <typename T> struct is_strand : std::false_type {};  
template <typename T> struct is_strand<asio::strand<T>> : std::true_type {};  
  
constexpr struct is_single_threaded_t  
{  
  template <typename T>  
    static constexpr bool is_applicable_property_v = asio::execution::is_executor_v<T>;  
  
  using polymorphic_query_result_type = bool;  
  
  template <typename T, typename = std::enable_if_t<is_strand<T>::value>>  
  static constexpr bool static_query_v = true;  
  
  template <typename T>  
  friend constexpr bool query(const T&, is_single_threaded_t) noexcept  
  {  
    return false;  
  }  
} is_single_threaded;  
  
int main()  
{  
  asio::io_context ctx;  
  
  auto ex1 = ctx.get_executor();  
  assert(asio::query(ex1, is_single_threaded) == false);  
  
  auto ex2 = asio::make_strand(ex1);  
  assert(asio::query(ex2, is_single_threaded) == true);  
  
  using any_ex_t = asio::execution::any_executor<is_single_threaded_t>;  
  
  any_ex_t ex3 = ex1;  
  assert(asio::query(ex3, is_single_threaded) == false);  
  
  any_ex_t ex4 = ex2;  
  assert(asio::query(ex4, is_single_threaded) == true);  
}  
```

---

## Comment 28

> Username: madmongo1  
> Created_at: 2020-06-26 07:50:15 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-650036232  

oh! we can make up new properties! I see.  
  
Many thanks.

---

## Comment 29

> Username: madmongo1  
> Created_at: 2020-06-29 10:10:33 UTC  
> Updated_at: 2020-06-29 10:11:23 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-651037985  

Hi @chriskohlhoff ,  
  
All working when compiled with gcc/clang but I am having problems with MSVC.  
  
In the test file `test/beast/core/bind_hander.cpp`, the `test_exectutor` is failing the `execution::is_executor` check.  
  
On Visual Studio 15, it fails the check on three counts:  
  
1: `execution::can_execute<>`  
  
2: `traits::equality_comparable<T>::is_valid`  
3: `traits::equality_comparable<T>::is_noexcept`  
  
2 & 3 can be mitigated by defining the preprocessor macro: `BOOST_ASIO_HAS_DEDUCED_EQUALITY_COMPARABLE_TRAIT` (I found this through a code search and just tried it on the off-chance it would help)  
  
However, 1 seems intractible.  
  
So there seem to be 2 problems:  
  
1. MSVC somehow incompatible witht he can_execute trait  
2. config is failing to set `BOOST_ASIO_HAS_DEDUCED_EQUALITY_COMPARABLE_TRAIT`  
  
Any guidance would be appreciated.  
  
Asio commit:  
`fcd09b9c (HEAD -> develop, origin/develop, origin/HEAD) Build execution tests.`  
  
Example compiler output:  
  
```  
...beast\\test\beast\core\bind_handler.cpp(440): error C2672: 'boost::asio::bind_executor': no matching overloaded function found [...\beast\cmake-build-vs2015-debug\test\beast\core\tests-beast-core.vcxproj]  
...\beast\test\beast\core\bind_handler.cpp(440): error C2893: Failed to specialize function template 'boost::asio::executor_binder<decay<_Ty1>::type,ExecutionContext::executor_type> boost::asio::bind_executor(ExecutionContext &,T &&,enable_if<std::is_convertible<ExecutionContext&,boost::asio::execution_context&>::value,void>::type *)' [...\beast\cmake-build-vs2015-debug\test\beast\core\tests-beast-core.vcxproj]  
...\beast\test\beast\core\bind_handler.cpp(440): note: With the following template arguments:  
...\beast\test\beast\core\bind_handler.cpp(440): note: 'ExecutionContext=boost::beast::bind_handler_test::test_executor'  
...\beast\test\beast\core\bind_handler.cpp(440): note: 'T=boost::beast::bind_handler_test::test_cb'  
```

---

## Comment 30

> Username: chriskohlhoff  
> Created_at: 2020-06-29 13:33:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-651123446  

For these older compilers expression SFINAE can be unreliable, so you need to help it out by defining the traits. For example:  
  
```cpp  
namespace asio {  
namespace traits {  
  
#if !defined(ASIO_HAS_DEDUCED_EXECUTE_MEMBER_TRAIT)  
  
template <typename F>  
struct execute_member<my_executor, F>  
{  
  static constexpr bool is_valid = true;  
  static constexpr bool is_noexcept = false;  
  typedef void result_type;  
};  
  
#endif // !defined(ASIO_HAS_DEDUCED_SET_ERROR_MEMBER_TRAIT)  
#if !defined(ASIO_HAS_DEDUCED_EQUALITY_COMPARABLE_TRAIT)  
  
template <>  
struct equality_comparable<my_executor>  
{  
  static constexpr bool is_valid = true;  
  static constexpr bool is_noexcept = true;  
};  
  
#endif // !defined(ASIO_HAS_DEDUCED_EQUALITY_COMPARABLE_TRAIT)  
  
} // namespace traits  
} // namespace asio  
```

---

## Comment 31

> Username: madmongo1  
> Created_at: 2020-06-29 14:45:40 UTC  
> Updated_at: 2020-06-29 14:52:07 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-651168487  

@chriskohlhoff thanks for the response. I'll park that one as it turns out we don't support vs2015.  
  
So I am one compile error away from being able to merge this PR.   
  
Problem when compiling the `example/http/server/coro//http-server-coro`  target:  
  
(looks like an oversight in spawn.hpp ?)  
  
```  
.\boost/asio/executor_work_guard.hpp(91): error C2039: 'on_work_finished': is not a member of 'boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>,boost::asio::execution::detail::blocking::never_t<0>,boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0>>,boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0>>,boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0>>,boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0>>,boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0>>>'  
  
... many many lines ...  
  
.\boost/asio/impl/spawn.hpp(483): note: see reference to function template instantiation 'void boost::asio::spawn<Function,Executor>(const boost::asio::strand<Executor> &,Function &&,const boost::coroutines::attributes &)' being compiled  
        with  
        [  
            Function=std::_Binder<std::_Unforced,void (__cdecl *)(boost::beast::tcp_stream &,const std::shared_ptr<const std::string> &,boost::asio::yield_context),boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::any_io_executor,boost::beast::unlimited_rate_policy>,const std::shared_ptr<const std::string> &,const std::_Ph<1> &>,  
            Executor=boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>,boost::asio::execution::detail::blocking::never_t<0>,boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0>>,boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0>>,boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0>>,boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0>>,boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0>>>  
        ]  
  
... many more lines ...  
```  
  
Interestingly, I can replicate this locally when compiling with b2.  
  
If I use cmake to build, I get a different error:  
  
```  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(354): error C2027: use of undefined type 'boost::asio::execution::detail::blocking::possibly_t<0>'  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(199): note: see declaration of 'boost::asio::execution::detail::blocking::possibly_t<0>'  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(307): note: see reference to class template instantiation 'boost::asio::execution::detail::blocking_t<0>' being compiled  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(364): note: see reference to class template instantiation 'boost::asio::execution::detail::blocking_t<I>' being compiled  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(355): error C2027: use of undefined type 'boost::asio::execution::detail::blocking::always_t<0>'  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(200): note: see declaration of 'boost::asio::execution::detail::blocking::always_t<0>'  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(356): error C2027: use of undefined type 'boost::asio::execution::detail::blocking::never_t<0>'  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(201): note: see declaration of 'boost::asio::execution::detail::blocking::never_t<0>'  
  
... etc ...  
  
```  
  
This one has me flummoxed.  
  
for reference, the command line:  
  
```  
C:\PROGRA~2\MICROS~1\2019\COMMUN~1\VC\Tools\MSVC\1425~1.286\bin\Hostx86\x86\cl.exe  /nologo /TP -DBOOST_ALL_STATIC_LINK=1 -DBOOST_ASIO_DISABLE_BOOST_ARRAY=1 -DBOOST_ASIO_DISABLE_BOOST_BIND=1 -DBOOST_ASIO_DISABLE_BOOST_DATE_TIME=1 -DBOOST_ASIO_DISABLE_BOOST_REGEX=1 -DBOOST_ASIO_NO_DEPRECATED=1 -DBOOST_ASIO_SEPARATE_COMPILATION=1 -DBOOST_BEAST_SEPARATE_COMPILATION=1 -DBOOST_COROUTINES_NO_DEPRECATION_WARNING=1 -D_CRT_SECURE_NO_WARNINGS=1 -D_SCL_SECURE_NO_WARNINGS=1 -D_SILENCE_CXX17_ADAPTOR_TYPEDEFS_DEPRECATION_WARNING -D_SILENCE_CXX17_ALLOCATOR_VOID_DEPRECATION_WARNING -D_WIN32_WINNT=0x0601 -I..\include -IC:\Users\hodge\github\boostorg\boost -I..\. -IC:\Users\hodge\github\Microsoft\vcpkg\installed\x86-windows\include /std:c++latest /EHsc /MDd /Zi /Ob0 /Od /RTC1 /MTd   /bigobj /permissive- /W4 /MP /showIncludes /FoCMakeFiles\lib-asio.dir\test\lib_asio.cpp.obj /FdCMakeFiles\lib-asio.dir\lib-asio.pdb /FS -c ..\test\lib_asio.cpp  
```

---

## Comment 32

> Username: chriskohlhoff  
> Created_at: 2020-06-30 12:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-651771976  

I can no longer reproduce this with the latest code on the asio `develop` branch.

---

## Comment 33

> Username: vinniefalco  
> Created_at: 2020-06-30 13:02:51 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-651776164  

> I can no longer reproduce this with the latest code on the asio develop branch.  
  
22 new commits will do that :)

---

## Comment 34

> Username: madmongo1  
> Created_at: 2020-06-30 13:49:55 UTC  
> Updated_at: 2020-06-30 14:07:38 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-651803373  

@chriskohlhoff visualk studio 2017 is looking better (tests still running, but examples now compile)  
  
However when building with visual studio 2019 I'm getting errors, simply by including `include/boost/asio/impl/src.hpp`  
  
Both 32 and 64-bit architectures.  
  
UPDATE: This error occurs when `cxxstd` is set to a value >11 only Both visual studio 2017 and 2019  
  
  
```  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(364): error C2027: use of undefined type 'boost::asio::execution::detail::blocking::possibly_t<0>'  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(209): note: see declaration of 'boost::asio::execution::detail::blocking::possibly_t<0>'  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(317): note: see reference to class template instantiation 'boost::asio::execution::detail::blocking_t<0>' being compiled  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(374): note: see reference to class template instantiation 'boost::asio::execution::detail::blocking_t<I>' being compiled  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(365): error C2027: use of undefined type 'boost::asio::execution::detail::blocking::always_t<0>'  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(210): note: see declaration of 'boost::asio::execution::detail::blocking::always_t<0>'  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(366): error C2027: use of undefined type 'boost::asio::execution::detail::blocking::never_t<0>'  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/blocking.hpp(211): note: see declaration of 'boost::asio::execution::detail::blocking::never_t<0>'  
  
... etc  
```

---

## Comment 35

> Username: madmongo1  
> Created_at: 2020-06-30 14:07:48 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-651814834  

@chriskohlhoff update above:  
UPDATE: This error occurs when `cxxstd` is set to a value >11 only Both visual studio 2017 and 2019

---

## Comment 36

> Username: madmongo1  
> Created_at: 2020-06-30 14:54:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-651846303  

@chriskohlhoff: new error:  
#define BOOST_ASIO_NO_TS_EXECUTORS=1  
compiler: clang-9 -fcoroutines-ts -std=c++20 -stdlib=libc++  
then compile `beast/test/core/detect_ssl.cpp`  
  
output:  
```  
In file included from /home/rhodges/github/boostorg/boost/libs/beast/test/beast/core/detect_ssl.cpp:21:  
In file included from /home/rhodges/github/boostorg/boost/boost/asio/co_spawn.hpp:469:  
/home/rhodges/github/boostorg/boost/boost/asio/impl/co_spawn.hpp:60:16: error: no template named 'executor_work_guard'; did you mean 'beast::detail::execution_work_guard'?  
    >::type> : executor_work_guard<Executor>  
               ^  
```  
  
Executor type is `asio::io_context::executor_type`  
  
failing here:  
  
```  
template <typename Executor>  
struct co_spawn_work_guard<Executor,  
    typename enable_if<  
      !execution::is_executor<Executor>::value  
    >::type> : executor_work_guard<Executor>  // <<<==== HERE  
{  
  co_spawn_work_guard(const Executor& ex)  
    : executor_work_guard<Executor>(ex)       // <<<==== AND HERE  
  {  
  }  
};  
```

---

## Comment 37

> Username: vinniefalco  
> Created_at: 2020-06-30 16:12:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-651895784  

Chris thank you very much for your generous investment in this pull request. We have incorporated it into #1999. And we will try to support `BOOST_ASIO_NO_TS_EXECUTORS` in Beast.

---

## Comment 38

> Username: chriskohlhoff  
> Created_at: 2020-06-30 23:19:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-652096976  

> UPDATE: This error occurs when cxxstd is set to a value >11 only Both visual studio 2017 and 2019  
  
I don't have boost checked out on this windows VM and I am unable to divine the compiler command line options from the appveyor output. Can you paste the full compiler command line for VS 2017, as I don't get this error in my regression test runners.

---

## Comment 39

> Username: vinniefalco  
> Created_at: 2020-06-30 23:59:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1994#issuecomment-652108525  

The pull request is here now -> https://github.com/boostorg/beast/pull/1999

---
