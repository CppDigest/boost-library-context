# #1450 - I/O executor work guard in echo_op example is excessive [Closed]

> Username: mabrarov  
> Created at: 2019-02-13 16:40:37 UTC  
> Updated at: 2019-02-17 05:13:02 UTC  
> Closed at: 2019-02-17 05:13:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1450  

Hi Vinnie,  
  
I'm sorry, this topic was discussed among Asio issues and Boost mailing list, but I just found the time to get involved (and I failed to find that old discussion).  
  
In your example (as well as in recent examples of Asio) of composed operation - `echo_op` - you have a guard for I/O context named [`work`](https://github.com/boostorg/beast/blob/1a80dd4560011cad2f2aa56bf7ee55f8fea752a6/example/echo-op/echo_op.cpp#L96). I read all the great documentation you created about composed operations and here are my thoughts on it:  
  
1. while in general this guard (`work` data member `echo_op::state` structure) is required  
1. it's useless (excessive) in `echo_op` example.  
  
Here is the reason for item 2:  
  
1. when `echo_op::operator()(boost::beast::error_code ec, std::size_t bytes_transferred)` method is executed (by I/O context) I/O context cannot stop because of lack of pending operations (because it's executing one of them right in the moment)  
1. `echo_op::operator()` **implicitly** guarantees that:  
   * it will add new pending operation(s) into I/O executor before this method will return control to I/O executor or   
   * it won't add new operation(s) into I/O executor if composed operation is considered completed (that's OK because we may stop if composed operation completed)  
  
You may take a look at composed operations of Boost.Asio used for free functions like `boost::asio::async_write` and `boost::asio::async_read`. They do not use such a guard for I/O context by the same reason - because of it's guaranteed by control flow.   
  
So for `echo_op` example we can:  
  
* either remove `work` guard because it's not needed  
* or add a comment describing that `work` is not needed for this example (because of control flow in `operator()` implicitly guarantees the same), but we prefer to have it because in generic case it may be needed and this example is like tutorial which suggests best practices.  
  
I understand that keeping of outstanding work for composed operations comes from Boost.Asio requirements but we need to implement them in intelligent way and not just adding useless guards (incrementing atomic counter and decreasing performance :)

---

## Comment 1

> Username: djarek  
> Created at: 2019-02-13 16:49:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463275475  

Note that `async_write` and `async_read` are incorrect in this regard - they should have the work guard. The lack of work guard is only observable when the I/O executor and handler executor do not share a common ExecutionContext.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-02-13 16:50:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463276009  

**echo_op** needs to be completely rewritten anyway to use the new utility and also follow best-practices.

---

## Comment 3

> Username: mabrarov  
> Created at: 2019-02-13 16:54:46 UTC  
> Updated at: 2019-02-13 18:05:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463277636  

@djarek,  
  
> they should have the work guard.  
  
Please, take a look at my explanation - there is no need to add guard if control flow of composed operation guarantees existence of outstanding work (pending operations) in I/O context.  
  
> The lack of work guard is only observable when the I/O executor and handler executor do not share a common ExecutionContext.  
  
You are (almost) correct (this case is never true for intermediate completion handlers of composed operation, so it's not applicable to compose operations except some cases), but it's not the case for `echo-op` example, so that example doesn't need this guard.  
  
The only case I can find out of my head when intermediate completion handler of composed operation **needs** work guard (for I/O context which composed operation is tied to - let's name it `A`) is the case when some of intermediate operations performed by composed operation do not produce outstanding work for `A` execution context, like waiting on some event in another reactor / proactor in asynchronous way with callback (note that `boost::asio::bind_executor` method doesn't add work / pending operation to the bound execution context).

---

## Comment 4

> Username: mabrarov  
> Created at: 2019-02-13 16:56:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463278400  

@vinniefalco    
   
> **echo_op** needs to be completely rewritten anyway to use the new utility and also follow best-practices.  
  
The same issue exists in Boost.Asio example - https://www.boost.org/doc/libs/1_69_0/doc/html/boost_asio/example/cpp11/operations/composed_5.cpp

---

## Comment 5

> Username: djarek  
> Created at: 2019-02-13 18:52:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463320395  

The TS does not prescribe whether a composed operation that calls more than 1 async suboperation has to have a work guard or not. However, we can infer certain properties of Executors from the TS:  
1. The work tracking mechanism informs the Executor of potential future submissions of completion handlers for execution (i.e., there will be a call to `dispatch()`, `post()` or `defer()`)  
2. An operation must not submit a completion handler for execution unless at least one work item is pending (where work item is either a completion handler that was submitted, but has not yet finished execution, or an active work guard).  
3. An operation must be correct if it is used in a composed operation that calls only one suboperation (i.e. caller operation does not maintain an I/O work guard).  
  
Given 2. and 3. we can deduce that a composed operation *MUST* maintain an I/O work guard if it performs a sequence of n async suboperations, where n > 1.  
  
Note that we proposed to Chris Kohlhoff a change that would allow composed operations to skip the work guard and rely on the work guard of "primitive" operations. This limits some (fairly odd) use cases, but simplifies the common scenario.

---

## Comment 6

> Username: mabrarov  
> Created at: 2019-02-13 20:36:41 UTC  
> Updated at: 2019-02-13 20:39:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463358828  

@djarek,  
  
Regarding:  
  
> 2\. An operation must not submit a completion handler for execution unless at least one work item is pending (where work item is either a completion handler that was submitted, but **has not yet finished execution**, or an active work guard).  
  
Refer to my initial message  
  
> 1\. when `echo_op::operator()(boost::beast::error_code ec, std::size_t bytes_transferred)` method is executed (by I/O context) I/O context cannot stop because of lack of pending operations (because it's executing one of them right in the moment)  
  
Doesn't my statement satisfies item 2? It would be helpful if @chriskohlhoff could comment on that. I'll be surprised if it's not satisfies - that would be illogical.  
  
> 3\. An operation must be correct if it is used in a composed operation that calls only one suboperation (i.e. caller operation does not maintain an I/O work guard).  
  
This is not applicable to this issue - we do not require caller to maintain I/O work guard - so we satisfies this requirement too.

---

## Comment 7

> Username: djarek  
> Created at: 2019-02-13 21:14:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463374187  

@mabrarov `echo_op::operator()(boost::beast::error_code ec, std::size_t bytes_transferred)` doesn't execute on the I/O executor, but on the handler's executor. The case you mention ("can't run out of work, because we're executing") only happens when the handler's executor is equal to the I/O executor. In the general case that is not necessarily true.

---

## Comment 8

> Username: mabrarov  
> Created at: 2019-02-13 22:51:53 UTC  
> Updated at: 2019-02-13 22:57:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463408430  

@djarek,  
  
> only happens when the handler's executor is equal to the I/O executor  
  
Yes, I agree. This is exactly the case we have in examples (Beast and Boost.Asio), isn't it? And this issue is about examples.  
  
> In the general case that is not necessarily true.  
  
Hmm... you are right but not - intermediate handler - `echo_op::operator()(boost::beast::error_code, std::size_t)` - will almost always (this is the core of composed operation approach) be executed in the same I/O context as composed operation (while user provided completion handler of composed operation may be bound to any other I/O executor). Exception(s) for this "rule" can be found in my [previous post](https://github.com/boostorg/beast/issues/1450#issuecomment-463277636).  
  
I appreciate if you could provide an opposite example. I'd like to clarify this item for myself.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-02-13 22:57:03 UTC  
> Updated at: 2019-02-13 22:57:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463409897  

@mabrarov So yeah, I've rewritten the example and I'll merge it in a couple of hours as-is unless you think it needs improvement or chnage. This is the commit: https://github.com/vinniefalco/beast/commit/ebc5246c473605b1454deeb99ae18a25e2a62a56  
  
Thanks!

---

## Comment 10

> Username: mabrarov  
> Created at: 2019-02-13 23:21:36 UTC  
> Updated at: 2019-02-13 23:22:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463416583  

@vinniefalco,  
  
It looks like re-written example just hides work guard inside `async_op_base` base class, while my thought is about needlessness of that work guard inside composed operation because of intermediate handlers (of that composed operation) are executed in I/O context of AsyncStream and so they - along with the flow which is used in composed operation - always keep at least one outstanding (asynchronous) operation in AsyncStream - provide "implicit" work guard such as mentioned by @djarek:  
  
> 2. An operation must not submit a completion handler for execution unless at least one work item is pending (where work item is either a completion handler that was submitted, but **has not yet finished execution**, or an active work guard).  
  
Do I misunderstand your changes? It's hard for me to read modern code with coroutines so I believe I could miss smth.

---

## Comment 11

> Username: mabrarov  
> Created at: 2019-02-13 23:40:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463421703  

Example of `proxy` in [Thinking Asynchronously](https://github.com/boostcon/2011_presentations/raw/master/mon/thinking_asynchronously.pdf) of @chriskohlhoff (https://github.com/boostcon/2011_presentations) needs an explicit work guard in `transfer_op` (work guard(s), because `async_transfer` composed operation looks like being tied to 2 AsyncStreams) only if `AsyncStream1` and `AsyncStream2` do not use the same I/O context (`io_service` in presentation of 2011 year).  
  
That's another example when explicit work guard is required (because composed operation doesn't guarantee at least one pending operation at any time for the bound I/O context).

---

## Comment 12

> Username: vinniefalco  
> Created at: 2019-02-14 00:11:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463429241  

@chriskohlhoff any ideas here?

---

## Comment 13

> Username: djarek  
> Created at: 2019-02-14 00:47:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463437515  

https://wandbox.org/permlink/b7BxBcxvCTre1x20  
@mabrarov You're forgetting that a handler may have an associated executor for which `dispatch()` does not always result in direct invocation. A user may have a use case in which their completion handlers occasionally have to block (e.g. read a large file), so they decide to associate an executor with their handler, so that the handler doesn't block I/O of other concurrent operations.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2019-02-14 00:53:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463439066  

@djarek People keep getting confused about this, myself included, so maybe you could just put together a very small program that proves that the executor is needed (without exotic types) and then we can link it.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2019-02-14 00:59:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463440433  

@djarek As far as I am concerned, this program: https://wandbox.org/permlink/b7BxBcxvCTre1x20 settles it once and for all, conclusively that the work guard is needed - thank you.

---

## Comment 16

> Username: mabrarov  
> Created at: 2019-02-14 07:58:28 UTC  
> Updated at: 2019-02-14 09:23:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463528920  

@djarek,  
  
> https://wandbox.org/permlink/b7BxBcxvCTre1x20  
  
This example implements [the case I wrote about](https://github.com/boostorg/beast/issues/1450#issuecomment-463277636) and doesn't cover the case we have in Boost.Asio and Beast examples (this issue is about Beast example which code confuses me):  
  
> The only case I can find out of my head when intermediate completion handler of composed operation **needs** work guard (for I/O context which composed operation is tied to - let's name it `A`) is the case when some of intermediate operations performed by composed operation do not produce outstanding work for `A` execution context, like waiting on some event in another reactor / proactor in asynchronous way with callback (note that `boost::asio::bind_executor` method doesn't add work / pending operation to the bound execution context).  
  
So I'm still in doubt why do we need the same guard in examples where intermediate handlers of composed operation produce outstanding work for I/O context which composed operation is tied to.  
  
In general - yes, we **may** need it. In particular examples I wrote about - we don't need it and confuse users (readers). We either need to remove useless stuff or add a comment describing its needlessness for the case of particular example and keep it just as a best practice.  
  
@vinniefalco,  
  
Having such work guard in some reusable (generic) library class - like in `async_op_base` base class (which is considered to be used by Beast users for construction of custom composed operations, isn't it?) - looks OK, while it may add some overhead (which is too small comparing to other stuff performed by Beast, so we may consider it as acceptable trade-off - I use the same approach in [ma::hadler_storage](https://github.com/mabrarov/asio_samples/blob/master/libs/ma_handler_storage/include/ma/handler_storage_service.hpp#L370) class) because the only way to implement work guard is to maintain atomic counter which increment and decrement are not free.  
  
If I understand your fix correctly (see above), then I believe you can close this issue as resolved / fixed.

---

## Comment 17

> Username: vinniefalco  
> Created at: 2019-02-14 13:58:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463636579  

> So I'm still in doubt why do we need the same guard in examples where intermediate handlers of composed operation produce outstanding work for I/O context which composed operation is tied to.  
  
The linked example https://wandbox.org/permlink/b7BxBcxvCTre1x20 is pretty self explanatory, without the work guard it malfunctions. Do you agree that the work guard is required? If yes, then if someone used a `thread_pool` with any beast composed operation, and beast did not have the guard then do you agree it would malfunction as well? If yes, then why would we want beast to malfunction in this scenario?

---

## Comment 18

> Username: mabrarov  
> Created at: 2019-02-14 14:11:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463640716  

> Do you agree that the work guard is required?  
  
Yes, I agree. Moreover - refer to my previous post - I described that example myself even before it was implemented by @djarek  :)  
  
> if someone used a `thread_pool` with any beast composed operation, and beast did not have the guard then do you agree it would malfunction as well?  
  
This issue is about specific example where work guard is not required (do you agree?) and existence of work guard confused me. If we talk about generic use case - then refer to my previous post for explanation of my understanding.

---

## Comment 19

> Username: vinniefalco  
> Created at: 2019-02-14 14:18:07 UTC  
> Updated at: 2019-02-14 14:18:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463643143  

> This issue is about specific example where work guard is not required (do you agree?)  
  
Ah!!! Yes, I think I agree.  
  
Lets be precise, the work guard is unnecessary only if  
  
    is_same<Executor1, Executor2>::value == true && ex1 == ex2  
  
?

---

## Comment 20

> Username: vinniefalco  
> Created at: 2019-02-14 14:28:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463647068  

@mabrarov how do you feel about this https://github.com/vinniefalco/beast/commit/cc527d7a309bba75300d25cd01c7f36f8005ae18

---

## Comment 21

> Username: mabrarov  
> Created at: 2019-02-14 14:33:49 UTC  
> Updated at: 2019-02-14 14:44:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463648756  

> ```cpp  
> is_same<Executor1, Executor2>::value == true && ex1 == ex2  
> ```  
  
Yes, this is required, but it is not sufficient to avoid explicit work guard.   
  
Another requirement looks like this: composed operation (the control flow of composed operation) should keep at least one outstanding / pending operation in I/O context which composed operation is bound to till the time composed operation completes. Intermediate operations and handlers of composed operation may often fulfill this requirement (as well as the 1st requirement described by you may be fulfilled in most of cases too).   
  
Refer to composed operations used by `boost::asio::async_write` and `boost::asio::async_read` for examples where both requirements are easily fulfilled (so easily that you even didn't mention lack of explicit work guard in that part of Asio code :)  
  
Anyway, please check the last sentences of [that post](https://github.com/boostorg/beast/issues/1450#issuecomment-463528920) - it looks like your fix for example moved work guard from example into library (reusable) code and in that case work guard is needed (because we can not guarantee that both requirements will be fulfilled with `async_op_base` class). If I understand fix correctly then it looks like this issue is resolved, while I appreciate if we could describe the case when work guard may be omitted in documentation for composed operations (but this is not related directly to this discussion, which can be used as a reference in future).

---

## Comment 22

> Username: mabrarov  
> Created at: 2019-02-14 14:39:12 UTC  
> Updated at: 2019-02-14 14:41:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463650624  

@vinniefalco   
  
> @mabrarov how do you feel about this [vinniefalco@cc527d7](https://github.com/vinniefalco/beast/commit/cc527d7a309bba75300d25cd01c7f36f8005ae18)  
  
You was too fast comparing to my typing :) Please check the 2nd requirement I described above (in previous post). If `async_op_base` is reusable library code (am I correct?) then we can not guarantee that 2nd requirement will be fulfilled, i.e. we should always create work guard if we talk about `async_op_base` class.

---

## Comment 23

> Username: vinniefalco  
> Created at: 2019-02-14 14:43:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463652203  

> Please check the 2nd requirement I described above (in previous post). If `async_op_base` is reusable library code (am I correct?) then we can not guarantee that 2nd requirement will be fulfilled, i.e. we should always create work guard if we talk about `async_op_base` class.  
  
Yes I think you might be right here, although I could add a function `async_op_base::engage_work()` or an additional constructor to allow the derived class to indicate that it always needs the guard. As you said, the atomic variables are not free.  
  
In your opinion, is the added complexity (and danger of misuse) worth saving the extra atomic operations?

---

## Comment 24

> Username: mabrarov  
> Created at: 2019-02-14 14:46:34 UTC  
> Updated at: 2019-02-14 14:51:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463653359  

> In your opinion, is the added complexity (and danger of misuse) worth saving the extra atomic operations?  
  
I answered this questions in asio_examples already - I believe that atomic counter overhead is acceptable trade-off (I agree about complexity and misuse).

---

## Comment 25

> Username: vinniefalco  
> Created at: 2019-02-14 15:53:22 UTC  
> Updated at: 2019-02-14 15:54:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463679534  

@mabrarov  Okay, how about this then https://github.com/vinniefalco/beast/commit/65cbc158cf53a78272ee35866e860b1e73959a85

---

## Comment 26

> Username: mabrarov  
> Created at: 2019-02-14 16:18:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463690064  

https://github.com/vinniefalco/beast/commit/65cbc158cf53a78272ee35866e860b1e73959a85#r32320802

---

## Comment 27

> Username: mabrarov  
> Created at: 2019-02-15 00:25:02 UTC  
> Updated at: 2019-02-15 00:39:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463857995  

@djarek,  
  
> `echo_op::operator()(boost::beast::error_code ec, std::size_t bytes_transferred)` doesn't execute on the I/O executor, but on the handler's executor. The case you mention ("can't run out of work, because we're executing") only happens when the handler's executor is equal to the I/O executor. In the general case that is not necessarily true.  
  
Could you please check this example - https://github.com/mabrarov/asio_samples/commit/71970aa29ef6ef31da8d3b1cca7dcf063b0c0c05#diff-285ab8460197331af2c0624ffcdf2c63 - if it demonstrates what you meant?   
  
If it's so then:  
  
1. There is no (was no) issue in Beast example... OK work guard is really not needed, but if we consider generic case and not the case we have in `main` function, then it's safer to have work guard. Anyway, comment in `echo_op` example could be helpful for those who may be confused by not needed but existing guard. I guess @vinniefalco added such comment.  
1. There is a bug in composed operations provided by Asio, like `boost::asio::async_read` where work guard is missing. This bug leads to `io_context` running out of work before composed operation completes if (user provided) completion handler of composed operation is bound to another instance of `io_context` (comparing to instance of `io_context` which composed operation is bound to)  
  
or (I don't like below, but it really looks like user trying to shoot his leg)  
  
It's not allowed for completion handler of composed operation to use different associated executor (not true for strands which are just wrappers around existing executor) than executor which composed operation is bound to (`io_context` associated with `AsyncStream` for `boost::asio::async_read`). And this should be documented.

---

## Comment 28

> Username: djarek  
> Created at: 2019-02-15 00:50:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463863700  

Yep, that's the situation I was talking about. I created an issue in ASIO about the work guards (https://github.com/boostorg/asio/issues/202).

---

## Comment 29

> Username: mabrarov  
> Created at: 2019-02-15 01:03:08 UTC  
> Updated at: 2019-02-15 01:09:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463866085  

@vinniefalco,  
  
Regarding overhead work guard adds if it's used in composed operation to prevent underlying `io_context` from running out of work:  
  
1. if composed operation uses move for intermediate handlers, like your `echo_op` does:  
   ```cpp  
   boost::asio::async_read_until(p.stream, p.buffer, "\r", std::move(*this));  
   ```  
   I'm not sure the same is used in composed operations provided by Asio like `boost::asio::async_read` - we need to review code of Asio  
1. and if work guard has optimized move constructor (should not increment outstanding work counter in `io_context` and should prevent decrement of the same counter in destructor of moved work guard)  
  
then overhead is just 1 increment and 1 decrement of atomic counter for the whole composed operation. That's completely acceptable. What do you think? We can review code of `asio::executor_work_guard` and if if doesn't meet 2nd criteria (see above) then propose a change into Asio.  
  
If changes in Asio code will be needed then @djarek could mention them as linked changes improving performance for https://github.com/boostorg/asio/issues/202

---

## Comment 30

> Username: mabrarov  
> Created at: 2019-02-15 01:06:22 UTC  
> Updated at: 2019-02-15 01:07:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463866698  

> 2\. and if work guard has optimized move constructor (should not increment outstanding work counter in `io_context` and should prevent decrement of the same counter in destructor of moved work guard)  
  
Just checked `boost::asio::executor_work_guard` template class from Boost 1.69.0 and it looks like this criteria is fulfilled by usage of `owns_` data member of `boost::asio::executor_work_guard`

---

## Comment 31

> Username: mabrarov  
> Created at: 2019-02-15 01:14:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463868264  

> 1\. if composed operation uses move for intermediate handlers, ... I'm not sure the same is used in composed operations provided by Asio like `boost::asio::async_read` - we need to review code of Asio  
  
I just checked code of `boost::asio::async_write` and it looks like moving intermediate handlers. @vinniefalco and @djarek, your review is appreciated too

---

## Comment 32

> Username: vinniefalco  
> Created at: 2019-02-15 01:29:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1450#issuecomment-463871324  

to be honest my mind is scrambled from staring at this for too long
