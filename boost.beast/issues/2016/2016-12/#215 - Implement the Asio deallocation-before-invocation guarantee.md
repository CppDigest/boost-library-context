# #215 - Implement the Asio deallocation-before-invocation guarantee [Closed]

> Username: harrishancock  
> Created at: 2016-12-27 17:48:37 UTC  
> Updated at: 2017-02-02 12:34:42 UTC  
> Closed at: 2017-01-10 23:48:15 UTC  
> Url: https://github.com/boostorg/beast/issues/215  

### TL; DR  
  
Great library! I love it. However, not deallocating all memory associated with a handler before making the upcall to that handler is problematic, as it prevents users from implementing composed operations on top of Beast's composed operations without knowing which invoked handlers are safe to move and which are not. [You already tripped over one such bug, it looks like](https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/accept.ipp#L261).  
  
There are a couple workarounds (make users copy their invoked handlers, or equivalently make copies of upcall handlers before invoking them, or just defer upcalls with `ios.post()`), but a full, performant fix is challenging. I have a few suggestions at the end.  
  
### The deallocation-before-invocation guarantee  
  
Beast supports the same custom memory allocation strategy as Asio, using the `asio_handler_allocate` and `asio_handler_deallocate` hooks. However, it does not implement the same [guarantee that Asio does](http://www.boost.org/doc/libs/1_62_0/doc/html/boost_asio/overview/core/allocation.html), namely:  
  
> The implementation guarantees that the **deallocation will occur before the associated handler is invoked**, which means the memory is ready to be reused for any new asynchronous operations started by the handler.  
  
And reiterated [here](http://www.boost.org/doc/libs/1_62_0/doc/html/boost_asio/reference/asynchronous_operations.html):  
  
> If the implementation needs to allocate storage for an asynchronous operation, the implementation will ... **deallocate the storage prior to the invocation of the handler** via asio_handler_invoke.  
  
I will refer to this guarantee as the "deallocation-before-invocation guarantee."  
  
### Beast does not implement this guarantee  
  
Beast's composed operations are implemented using stateful handlers (`accept_op`, `response_op`, `write_op`, etc.), which are lightweight wrappers around a `shared_ptr<data>`, where `data` is the type containing the operation's state. The `shared_ptr<data>` object is allocated using the Asio custom memory allocation strategy.  
  
Since the handler `h` passed to the operation's initiating function (I will refer to this as its "upcall handler") lives in the `data` object, `h` is therefore stored in memory that was allocated using its own allocation strategy. In order to implement Asio's guarantee when invoking `h`, `h` would have to be moved/copied out of the `data` object to a new object `h'`, the `data` object destroyed and deallocated, then `h'` finally invoked.  
  
When a Beast composed operation completes, the last line is typically something like `d.h(ec)`, where `d` is the `data` object, `h` is the operation's upcall handler, and `ec` is the final `error_code`. However, `d` is managed by a `shared_ptr` allocated using `h` at the time of `h`'s invocation! This means that at the time that `h` is invoked, not all memory allocated using its hooks has yet been deallocated.  
  
### This is a problem  
  
There are two main consequences of this:  
  
1. As the Asio docs point out, the next asynchronous operation cannot re-use the memory from the just-completed asynchronous operation. This is an important, but not urgent concern.  
  
2. The implementation of `h.operator()` cannot safely move itself (e.g., pass `std::move(*this)` to the next asynchronous operation), because `h` is still needed for deallocation of the previous operation's state.  
  
   The reason for this is not obvious. After all, every composed operation's state stores its own copy of its upcall handler in the form of an allocator in the `shared_ptr<data>`'s control block, so why would it matter if the invoked copy of the handler gets moved away?  
  
   The problem arises when there are multiple levels of composed operations: i.e., composed operations implemented in terms of other composed operations. The handler copy in the allocator in the bottom-most composed operation's `shared_ptr<data>` object does not forward its `asio_handler_deallocate` call to the next-higher operation's `shared_ptr<data>` allocator, but to the next-higher operation's handler-to-be-invoked.  
  
   You can make as many copies of an upcall handler as you want. But if it contains a `shared_ptr` holding *its* upcall handler, then you are *not* making copies of *its* upcall handler. There remains one and only one of those, and it is required by the `asio_handler_(de)allocate` forwarding chain.  
  
   I'll work through an example. Say `op0` is the stateful handler for some top-level composed operation. It is implemented in terms of `op1`, which is implemented in terms of `op2`. Each of these handlers stores two copies of the operation above it, so:  
  
   ```  
                           op0mem{data{h}, alloc{h'}}  
                            ^  
                            |\---------\  
                            |           \  
               op1mem{data{op0}, alloc{op0'}}  
                ^  
                |\---------\  
                |           \  
   op2mem{data{op1}, alloc{op1'}}  
    ^  
    |  
   op2  
   ```  
  
   where:  
   - `h` is the user's handler object that may or may not implement a custom allocation strategy  
   - `opX` is the copy of the handler stored in `opX+1`'s `data`, to be invoked upon completion of `opX+1`  
   - `opX'` is the copy of the handler stored in the allocator that lives in the control block of `opX+1`'s `shared_ptr<data>`  
   - `opXmem` is the unique block of memory co-owned by all `opX`, `opX'`, etc., containing both `data` and the control block  
  
   Assume `op2` has just completed, that it was the last sub-operation of `op1`, and that `op0` wants to continue on and call another asynchronous operation. Trace what happens:  
  
   1. The Asio event loop puts `op2` on the stack and invokes it.  
   2. `op2` invokes `op1`, which finds itself complete and invokes `op0`.  
   3. `op0` initiates another asynchronous operation, passing itself by-move as the handler. `op0` is now in a **moved-from state**.  
   4. Control flow returns to the Asio event loop, and `op2` goes out of scope and gets destroyed.  
   5. `op2mem`'s reference count drops to zero and it destroys `op1`, and everything else in `data`.  
   6. `shared_ptr` deallocates `op2mem`. It does so by copying the allocator containing `op1'` to a new allocator containing `op1''`, then calling `alloc.deallocate(...)` which forwards to `asio_handler_deallocate(..., &op1'')`.  
   7. `asio_handler_deallocate(..., &op1'')` forwards to `asio_handler_deallocate(..., &op0)` (*not* to `&op0'`, which is hidden away in an allocator).  
   8. `asio_handler_deallocate(..., &op0)` forwards to `asio_handler_deallocate(..., &h)`. This involves dereferencing a nullptr because `op0` is in a **moved-from state**!  
   9. All hell breaks loose.  
  
   For a concrete example of this, replace `op0`, `op1`, and `op2` with instances of `accept_op`, `read_op`, and `parse_op`, respectively. This is almost certainly the situation which causes this crash:  
   https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/accept.ipp#L261  
  
Another consequence that follows from this is that it becomes impossible (without ugly workarounds) for the operation represented by `h` to itself implement the deallocation-before-invocation guarantee, because that would require moving and deallocating the storage for its own wrapped handler, which operations underneath may still depend upon for *their* deallocation. In other words, lower level operations should implement the guarantee in order for higher level operations to implement it, too.  
  
### How to fix it  
  
A quick and dirty workaround might be to make a copy of the handler on the stack and invoke that copy, rather than the copy in the `data` object. This would not implement the guarantee, but it would at least allow higher-level code to move its handlers without risking crashes.  
  
The strategy that Asio's own composed operations use to implement the guarantee seems to be to avoid allocating memory at all costs -- most (all?) of its stateful handlers just keep what would be the contents of `data` inside the `*_op` structure itself. Some of Beast's composed operations might be able to do this, but it's pretty obviously not a generalizable technique.  
  
A few naive strategies come to mind:  
  
1. Defer the invocation of `h` by changing every invocation of `d.h(ec)` to:  
   ```c++  
   ios.post(bind_handler(std::move(d.h), ec));  // was d.h(ec)  
   ```  
   and hope that all copies of the `shared_ptr` get destroyed before the handler is invoked. This is pessimistic in terms of performance and I suppose relies on implementation details of Asio for correctness, but it's an easy fix.  
  
2. Manually deallocate `data` by changing every invocation of `d.h(ec)` to:  
   ```c++  
   complete_op(d_, ec);  // was d.h(ec)  
   ```  
   where `complete_op` is something like  
   ```c++  
   template <class Data, class... Args>  
   void complete_op(shared_ptr<Data>& d, Args&&... args) {  
     if (d.use_count() == 1) {  
       auto h = std::move(d->h);  
       d.reset();  
       h(ec, std::forward<Args>(args)...);  
     }  
     else {  
       d.reset();  
     }  
   }  
   ```  
   This is a little more aggressive in terms of performance, and it shows the intent more clearly. It relies on the fact that the operation's state `shared_ptr` is not accessible from more than one thread (`use_count()` is only approximate otherwise). If a composed operation contains a code path which allows a copy of the `shared_ptr` to go out of scope without first checking to see if it should be invoked, then the operation might accidentally never invoke its handler, so it's difficult to prove correct.  
  
3. Use a `boost::intrusive_ptr` instead of a `std::shared_ptr`, store the final results of every operation (e.g. `ec`) inside `data`, replace every instance of `d.h(ec)` with `d_.reset()`, and implement `intrusive_ptr_release` to do the necessary deallocation-before-invocation juggling.  
  
   This technique would definitely call the handler, no matter what, even if a copy of the `intrusive_ptr` is allowed to go out of scope in some code path, although this "safety" comes with the cost of invoking a possibly throwy handler inside `~intrusive_ptr`, which is `noexcept(true)`. Another drawback is that it conflates resource management with handler invocation (handlers get invoked by a release function -- imagine that in your stack trace).  
  
I'm not really thrilled by any of those options, though in my own code I use the `intrusive_ptr` technique I described. Perhaps some better technique exists?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-12-27 18:14:44 UTC  
> Updated at: 2016-12-27 18:20:12 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-269362082  

Thank you for this wonderfully detailed report. The situation you are describing makes perfect sense. I agree that this is a problem, it seems that in my careful scrutiny of all the asio legalese I missed this important point. Very grateful for your explanation which brings clarity to it.  
  
I've often wondered if I did the right thing putting the composed operation state in a `shared_ptr`. And I have also noticed that asio indeed goes out of its way to avoid allocating state for its operations. But when I look at the operations in Beast, the operations are sufficiently complex that avoiding memory allocation is impossible. Do you see a way to rewrite some of the operations to avoid allocations?  
  
One alternative:  
  
* Allocate the storage for the `shared_ptr` using a regular allocator.  
  
What do you think about that?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-12-27 18:37:36 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-269364940  

Here's another idea:  
  
Specify a custom deleter when allocating the `data` object via `std::allocate_shared`. This deleter will check a bool indicating whether the handler should be invoked. If `true` then move the handler to the stack, destroy the managed object, deallocate the memory using the hook, then invoke the handler. Parameters to the handler will also have to be stashed in `data` and copied out:  
  
```  
void  
delete_op(data* p)  
{  
    if(p->invoke)  
    {  
        auto h = std::move(p->h);  
        auto const ec = p->ec;  
        p->~data();  
        boost_asio_handler_alloc_helpers::  
            deallocate(p, sizeof(*p), h);  
        h(ec);  
    }  
    else  
    {  
        p->~data();  
        boost_asio_handler_alloc_helpers::  
            deallocate(p, sizeof(*p), h);  
    }  
}  
```

---

## Comment 3

> Username: harrishancock  
> Created at: 2016-12-27 18:56:20 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-269367289  

I think putting the state in a `shared_ptr` is totally reasonable. Asio's own examples use this technique, if I remember right, and it's effectively the solution I came to in my own code. However, it does make implementing the guarantee challenging, to say the least.  
  
> Do you see a way to rewrite some of the operations to avoid allocations?  
  
No, I agree with you -- I think they're sufficiently complex that some of them at the very least will need allocations. To avoid all allocations, you'd probably need to force the user to pass in scratch work space for several of the operations, which would be a real shame, as such an API would turn off the many users who could otherwise benefit from the library and don't care about custom allocation.  
  
> Allocate the storage for the shared_ptr using a regular allocator.  
  
I.e., don't use the custom memory allocation scheme for the `shared_ptr`? I think this might be the best way to go, actually, at least for the short term until someone comes up with a better idea. It would increase safety of the library at the expense of some theoretical memory fragmentation. As a user, I would totally accept that. On the other hand, I'm sure there exists someone, somewhere who relies on the custom memory allocation scheme, and for whom this would be a dealbreaker. It might become a sticking point in Boost review, too.  
  
Regarding the deleter idea, a few problems:  
- I don't think `std::allocate_shared` can take a deleter. The `shared_ptr` constructor can take a pre-allocated pointer, an allocator (to allocate the control block), and a deleter, however.  
- I don't think `shared_ptr`'s specification guarantees that the control block is deallocated before the deleter is invoked, so invoking the handler in the deleter would still risk invoking a handler with memory still allocated. This could be worked around by not specifying the custom `handler_alloc`, but just letting `shared_ptr` manage its control block with the default allocator, as in your first idea.  
- The C++ standard states something like, "the expression `d(p)` shall not throw exceptions", so invoking a possibly throwy handler in the deleter might be dangerous (similar problem that my `intrusive_ptr` idea suffers from). This could be worked around by `post`ing the handler, but if you do that, you probably don't need to go to the trouble of passing in a deleter.

---

## Comment 4

> Username: harrishancock  
> Created at: 2016-12-27 19:09:52 UTC  
> Updated at: 2016-12-27 19:14:58 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-269368940  

One more idea: write a custom smart pointer which is aware of, and has specific storage for, an `io_service&`, an upcall handler, and the arguments to bind to that upcall handler. If the last copy of itself is destroyed with an explicit call to `reset()`, it just invokes the upcall handler directly. If it's destroyed passively in the destructor, it `post`s the upcall handler to avoid exceptions thrown in the dtor, or placing the dtor in the stack trace.  
  
Edit: such a smart pointer could be implemented as a lightweight wrapper around `intrusive_ptr`, I think.  
  
It feels like a nuclear option, though. I still like the "just use a regular allocator" idea best for the short term.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-01-02 14:25:28 UTC  
> Updated at: 2017-01-02 18:58:58 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-269978950  

How about if we do this, replace `std::shared_ptr<T>` with `beast::detail::mutual_ptr<T>`, with the following features:  
* Manages the lifetime of an object of type `T`  
* Reference counted, without using atomics (i.e. non thread-safe)  
* All `mutual_ptr` that manage the same object are threaded onto a doubly linked list  
* `mutual_ptr::reset_all` destroys the managed object and frees the memory, iterates the list of all instances and clears their pointer so they now do not have a managed object  
  
Composed operations will contain `mutual_ptr` instead of `shared_ptr`. Before the upcall, the handler and any necessary arguments are moved to the stack. Then `reset_all` is called to destroy all instances and deallocate the memory.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-01-02 17:27:06 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-269999425  

You can try it here  
https://github.com/vinniefalco/Beast/commits/mutual_ptr

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-01-02 20:25:53 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270016754  

Alright, this didn't work but it was close. Working on a refinement now...disregard that branch.

---

## Comment 8

> Username: harrishancock  
> Created at: 2017-01-02 20:43:45 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270018370  

Your first stab looked awesome, and I think your overall approach is a beautiful solution!  
  
You said disregard, but I already read the code, so here's some comments on the mutual_ptr branch that you may or may not find useful for your next refinement:  
  
- Is the explicit reference count necessary? That is, could it instead be implicit in the number of nodes in the linked list?  
- Using Boost.Intrusive might simplify the code and make it more maintainable. Downsides might be: learning curve of using that library (I've never used it), and a dependency on another library, even though it's Boost.  
- Move self-assignment isn't guarded against. Wouldn't that cause the pointer to destroy the managed object prematurely? Untested, just a thought.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-01-02 20:51:57 UTC  
> Updated at: 2017-01-02 20:56:08 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270019068  

@harrishancock Thanks! The problem with this approach is that the `handler_alloc` allocator which is used to manage memory, contains a handler which directly or indirectly holds a `mutual_ptr`. The call to `reset_all` resets the contained pointer, and then the allocator can no longer be used to free the memory.  
  
My next approach is to create a new managed pointer class templated on `Handler` instead of `Alloc`.This way we avoid the problems with `handler_alloc`. The managed object will include the handler, so the smart pointer can provide an `invoke` member which in one step takes care of both deallocation and making the final upcall. To answer your questions:  
  
* Now that I think about it, you're right about the reference counts. Note, that this doubly linked list has no explciit head or tail. But we can determine if we're the last instance by checking that `next_` and `prev_` are both `nullptr`.  
  
* I'm very familiar with Boost.Intrusive, and Beast is already using it (see `fields`). Its a great library! But since we don't have a head and tail, there's no actual container just the links. So we can't use Boost.Intrusive here.  
  
* You can get yourself into a self-copy situation fairly easily but a self-move requires more deliberate effort. Move special members are supposed to be _fast_ (for example, consider vector insertion). Since self-move is impossible in correct code, its best to leave out the check (from @HowardHinnant).

---

## Comment 10

> Username: harrishancock  
> Created at: 2017-01-02 21:36:33 UTC  
> Updated at: 2017-01-02 21:37:18 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270022847  

@vinniefalco Thanks for the answers (particularly the self-move enlightenment)!  
  
I'm still wrapping my head around the problem you described, though I can say I've boxed myself into similar-sounding situations while dealing with this issue. A general, robust solution to this problem will be a huge benefit to people writing composed ops. :)

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-01-02 21:51:15 UTC  
> Updated at: 2017-01-02 21:51:27 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270024022  

>A general, robust solution to this problem will be a huge benefit to people writing composed ops. :)  
  
Yes, that's exactly what I am shooting for here.  
  
>...people writing composed ops.  
  
People write composed operations? The more you can tell me about your particular use-case (especially if its published on GitHub), the better I will be able to tailor Beast to real-world uses. I haven't gotten a tremendous amount of feedback about Beast (not to sound grand but probably because the library was already well designed). But the more information is presented, the better I will be able to make critical design choices.

---

## Comment 12

> Username: harrishancock  
> Created at: 2017-01-03 01:16:01 UTC  
> Updated at: 2017-01-03 01:16:26 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270038944  

Certainly. My company's WebSocket-related code is sadly closed-source, so this might not be as useful as you're hoping, but here's my story:  
  
### High level  
  
My company ([Barobo](https://barobo.com)) sells Arduino-compatible robots to K-12 classrooms that can be controlled by JavaScript served from our web site, [linkbotlabs.com](https://linkbotlabs.com). Our current generation robots are not internet-enabled, however -- instead, they communicate with a radio transceiver that's plugged into a Raspberry Pi via USB. The Raspberry Pi runs a daemon which acts as a hub, routing messages from the LAN to robots, and vice versa.  
  
A typical situation might involve a classroom of 30 Chromebooks communicating with 15-30 robots via a single RPi. The Chromebooks communicate with the daemon, `linkbotd`, via WebSockets, which is where Beast might come in (currently it uses websocketpp, which I chose before I became aware of Beast). `linkbotd` communicates with a ZigBee transceiver as a USB-CDC serial device (e.g., /dev/ttyACM0 on Linux, or a COM port on Windows). The transceiver communicates with the robots via a proprietary radio protocol -- we use ZigBee hardware, but not the protocol itself.  
  
While `linkbotd`'s current target is primarily the RPi, it began life on Windows and Mac. Because of this, during its initial development I evaluated many different cross-platform serial port libraries, and Asio's was the only one I found that wasn't fundamentally broken in some way. During this research, I learned Asio's asynchronous operation model and realized that I could implement all of the daemon's communications using Asio, using composed operations. Such an architecture would (and did) end up being single-threaded, memory-efficient, and cross-platform, which was all highly attractive to me.  
  
### Lower level  
  
My first attempt used stackful coroutines (`asio::yield_context_t`), but a requirement arrived that I port `linkbotd` to a Linux platform with a CPU for which Boost.Coroutine didn't yet exist, so I rewrote everything using a big hairball of nested lambdas. I hated that.  
  
My next attempt at straightening out the mess used the stackless `asio::coroutine`. You're probably familiar with it -- it uses Duff's Device to implement coroutines, so the end result is roughly equivalent to your usage of `switch (d.state)` in your `*_op::operator()` implementations. While the WebSocket-related code is in the core `linkbotd` executable code, which is closed-source, some of our support libraries are publicly available. You can see an example of an asynchronous operation initiating function that uses my `asio::coroutine`-based infrastructure here:  
  
https://github.com/BaroboRobotics/usbcdc/blob/master/include/usbcdc/asio/linux/monitor.hpp#L108-L141  
  
`asyncDevices` produces a set of `/dev/ttyACMX` devices representing all USB-CDC devices plugged into a Linux box, internally implemented by parsing the stdout of `udevadm`. Everything inside the lambda's capture list would be equivalent to the contents of one of Beast's `data` structures. The `async_completion` instantiation and `intrusive_ptr` allocation is hidden behind the call to `asyncDispatch`, implemented here:  
  
https://github.com/BaroboRobotics/cxx-util/blob/master/include/util/asio/operation.hpp#L199-L212  
  
(Ignore the `v1` namespace -- it was an earlier attempt that is hanging out until I finish refactoring/removing old code.)  
  
Some observations:  
  
- `Operation` is a stateful handler roughly equivalent to Beast's `*_op` structures. It contains an `intrusive_ptr` to its `OperationState`, which contains:  
   1. The results of the composed operation.  
   2. The state and `operator()` of the composed operation (`mCoroutine`), roughly equivalent to Beasts's `*_op::data` structures. In my code, this is typically a lambda.  
   3. The upcall handler of the composed operation.  
   4. The `state` variable is inside the `asio::coroutine`, which lives in the `Operation` handler, not the operation state. In Beast terms, this would be like the `state` variable living in `*_op` rather than `data`. This allows coroutines to fork (as required in an accept loop, or when implementing a timed operation), yet have access to the same shared state. This works with care, but in retrospect I think it was a design mistake, and I hope to come up with a more robust way to do operation forking.  
  
- The instantiation of `async_completion` is hidden behind `asyncDispatch`. I did this to reduce the amount of boilerplate in the numerous `async*` initiating functions littering my code, but in retrospect it was an obvious mistake, because the members of the lambda capture list don't have access to `asio_handler_(de)allocate`.  
  
- Since the state of the composed operation is typically a lambda, `operator()` of the stateful handler is implemented at the `data` level in my code instead of the `*_op` level.  
  
   This has the disadvantage that I have to pass an rvalue reference to the operation's stateful handler to the operation's `operator()` as the first parameter (e.g., the `auto&& op` parameter to `asyncDevices`'s lambda implementation). Internally, the operation passes itself to sub-operations by passing `std::move(op)` instead of `std::move(*this)`, so the damage isn't too bad.  
  
   It has the advantage that all the `asio_handler_*` hook forwarding is implemented in one place (`Operation`), rather than once for each composed operation. I considered this boilerplate reduction to be a big win. I don't actually use the custom allocation scheme that Asio provides save for the occasional instrumentation (not very useful yet thanks to the `async_completion`-related bug I mentioned in a previous point), so I didn't want to spend too many lines of code on it. I also didn't want to forget about the hooks, though, if only for completeness' sake.  
  
You can see the (very messy, WIP) beginnings of how I'm using Beast using this infrastructure here:  
  
https://github.com/BaroboRobotics/cxx-util/blob/master/tests/rpc-test.cpp#L146  
  
### Conclusion  
  
I mentioned that right now we're using websocketpp for our WebSocket communications, but that I'd like to use Beast. The main reason I'd like to switch is because Beast seems far more slavish in being compatible with Asio's asynchronous operation model, while websocketpp's Asio-compatibility seems hamstrung by its design choice to support iostreams as a transport, and it seems unaware of Asio's asynchronous operation model (also, its documentation leaves me a bit speechless). Consequently, it's been harder to integrate into my own codebase. You can [see my shims here](https://github.com/BaroboRobotics/cxx-util/tree/master/include/util/asio/ws). Beast looks like it'd be a snap.  
  
Not to inflate your ego, but I concur that the reason you've not gotten too much feedback is probably because Beast is well-designed from the get-go. It's probably telling that my reasons for switching to Beast are all along the lines of "websocketpp just didn't do it right" (but I feel bad for bashing it -- it's a venerable project which really came through for me when I needed it!). You took a winning model in Asio, grokked it, and ran with it. I think the critical design choices you've made so far have been spot-on. The deallocation-before-invocation issue has been my only hiccup so far -- if I run into more, I'll certainly share them.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-01-03 01:49:30 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270040943  

>Beast seems far more slavish in being compatible with Asio's asynchronous operation model  
  
Yes that's a very accurate assessment. I want Beast to be a natural extension of Asio. Its more of a building block than an end-user facing library (just like Asio). Although the WebSocket module is more high level than the HTTP support. What's missing (and what I certainly don't ever plan on adding) is a full featured server that supports WebSocket and HTTP - but I encourage other people to build such general purpose, higher level functionality on top of Beast. That's why I am dedicated to providing the deallocate-before-invocation guarantee - as you pointed out, you cannot chain composed operations together to create larger composed operations unless this is fixed.  
  
The shortcomings of websocketpp in our own project were the motivation behind Beast, see:  
http://vinniefalco.github.io/beast/beast/design/websocketpp.html  
  
The usage information you have provided is very handy, it helps inform me about what kinds of tests I might add and also how people are using what's there - thanks for that.

---

## Comment 14

> Username: harrishancock  
> Created at: 2017-01-03 18:57:58 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270193398  

Vinnie, I wanted to add that I never did grasp the concern you had with your original mutual_ptr branch. Even if the current operation's (`op1`) upcall handler (`op0`) is implemented in terms of a `mutual_ptr`, `op0`'s `mutual_ptr` wouldn't get reset until after `op1`'s was already reset. In other words, it seems to me that the very dealloc-before-invoke guarantee that you implemented would guarantee that your concern about resetting the `mutual_ptr` inside `handler_alloc` couldn't be a problem, unless some sub-operation somewhere is not implementing the guarantee.  
  
```  
            op0mem{data{h}, alloc{h'}}  
             ^  
             |\--------\  
             |          \  
op1mem{data{op0}, alloc{op0'}}  
 ^  
 |  
op1  
```  
  
1. `op1` moves `op0` out of its `data` to the stack. Both `op0` and `op0'` are still valid.  
2. `op1` calls `op1.d_.reset_all()`, which deallocates `op1mem` using `op0'` (which is still valid, because `op0.d_.reset_all()` wasn't called yet). All copies of `op1` are now invalid and it has fulfilled its responsibility under the guarantee.  
3. `op1` invokes `op0` (still valid, because it's on the stack).  
4. `op0` moves `h` out of its `data` to the stack. Both `h` and `h'` are still valid.  
5. `op0` *now* calls `op0.d_.reset_all()`, which deallocates `op0mem` using `h'` (still valid, because `h.d_.reset_all()` wasn't called yet, assuming `h` holds a `mutual_ptr`). All copies of `op0'` are now invalid and it has fulfilled its responsibility under the guarantee.  
6. `op0` invokes `h` (still valid, because it's on the stack).  
  
At any rate, I tested it. It seemed to work fine with my test code -- my operations could pass themselves by move to their next sub-operations without nullptr dereferences, and the `std::move(*this)` line in accept.ipp that used to crash now works correctly. I logged all memory allocations and deallocations, and the guarantee implementation looked like it worked perfectly to me using `mutual_ptr`.  
  
That said, I didn't exercise the library much beyond setting up and tearing down WebSocket connections. Was there some unit test that failed with `mutual_ptr`?

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-01-03 19:04:08 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270195044  

Yes, there were tests that failed. The problem with the original `mutual_ptr` is that the type-erased allocator (`beast::handler_alloc`) itself contains a copy of the handler. When `reset_all` is called, this clobbers that handler tucked away inside the allocator.  
  
I have developed a new container `handler_ptr` which works just like `mutual_ptr`, except that it does not try to use a C++ style allocator. Instead, it requires that the managed object of type `T` contains a data member called `handler`. The implementation of `handler_ptr` calls the allocation and deallocation hooks directly, using this handler. This allows the smart pointer to implement `invoke` properly. I have this thing working and passing tests, I'm just working through an annoying MSVC compiler bug:  
  
https://github.com/vinniefalco/Beast/blob/mutual-ptr-2/include/beast/core/handler_ptr.hpp  
https://github.com/vinniefalco/Beast/blob/mutual-ptr-2/include/beast/core/impl/handler_ptr.ipp

---

## Comment 16

> Username: vinniefalco  
> Created at: 2017-01-03 20:16:31 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270212962  

My bad, I gave bad advice, @HowardHinnant has set me straight. This was wrong:  
  
>Move special members are supposed to be fast (for example, consider vector insertion). Since self-move is impossible in correct code, its best to leave out the check  
  
The rule is that move assignment needs to leave both objects in valid states (i.e., safe to destroy or re-assign). They don't necessarily have to make sense to the program but it shouldn't crash. In the case of `handler_ptr` I do need to put the check in there, otherwise `t_` could be left pointing to a destroyed object.

---

## Comment 17

> Username: harrishancock  
> Created at: 2017-01-03 20:38:02 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270218010  

Right, I see! That's a different gotcha than the mere self-nullification concern I had considered. Thanks for passing that along.  
  
`handler_ptr` looks like a nice solution, and even easier to integrate. I'll give it a whirl when you're done with it! :)

---

## Comment 18

> Username: vinniefalco  
> Created at: 2017-01-04 03:35:21 UTC  
> Updated at: 2017-01-04 03:37:12 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270287950  

This problem is way harder than I thought. `handler_ptr` cannot possibly work, because different threads can access different instances of `handler_ptr` which manage the same object. For example, one thread could be performing a copy while the other thread destroys. I'm not talking about the _same_ `handler_ptr`, but two _different_ instances of `handler_ptr` which manage the same object. The linked list operations screw things up.  
  
Clearly, a `mutex` would solve the problem. But where would we put it? We can't put it inside the managed object because that would create an unsolvable race. And it can't go into the `handler_ptr`. The best I can think of is to have a handful of global mutexes and deterministically pick one. Fairly ugly. This is a problem.  
  
Here's an example of code that fails. In this example, the call to `post` happens on a thread not associated with an `io_service`:  
```  
struct handler {  
  ...  
  handler_ptr<data> hp_;  
  void operator()() const;  
};  
handler h(...);  
ios.post(h);  
```  
  
It looks so simple? How can it fail? Well, consider that `~handler_ptr()` is called when `h` goes out of scope. But while that destructor executes we might also be destroying the copy that went in the `io_service` queue, and from another thread. This will screw up the linked list, which is not protected by any synchronization.

---

## Comment 19

> Username: harrishancock  
> Created at: 2017-01-04 04:02:37 UTC  
> Updated at: 2017-01-04 04:05:49 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270290429  

> ... different threads can access different instances of handler_ptr which manage the same object.  
  
Ouch. I was vaguely worried about this, but couldn't come up with a concrete example of when it might occur. On the other hand, the legalese doesn't seem to provide protection from it *not* occurring. [Edit: your example makes sense. Crap.]  
  
I haven't entirely thought this through, but perhaps a mutex could live in an implementation of an `IoObjectService`, say `handler_ptr_service<H>`? This would be "better" than a global mutex insofar as it would be `io_service`-specific, rather than global, but the amount of code required would probably balloon.  
  
For a non-copyable idea of what I mean, see: https://github.com/mabrarov/asio_samples/tree/master/libs/ma_handler_storage/include/ma

---

## Comment 20

> Username: vinniefalco  
> Created at: 2017-01-04 04:06:49 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270290765  

This installs a service in the `io_service`?!

---

## Comment 21

> Username: harrishancock  
> Created at: 2017-01-04 04:16:21 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270291518  

Yup. I know. :(  
  
Consider this, though: (correct me if I'm wrong, but) the only reason the resource-owning `handler_ptr`, `mutual_ptr`, `shared_ptr`, `intrusive_ptr` solutions are our go-to solutions is because the `io_service` they run on might get destroyed *before* they get run, in which case we need to rely on RAII to clean up their resources.  
  
The alternative to relying on RAII for resource cleanup in case handlers are never invoked is to use `io_service`'s `shutdown_service` hook. If we did that, we could pass around raw pointers, and it wouldn't matter -- the handlers wouldn't get destroyed transitively by their superobject's destructors, but they *would* get destroyed nonetheless, either through invocation or by `shutdown_service`.  
  
That's all I wanted to point out. This cure may well be worse than the disease.

---

## Comment 22

> Username: vinniefalco  
> Created at: 2017-01-04 04:19:37 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270291784  

I never did understand how the `io_service` services work, are you saying that I can associate a handler with data in the `io_service`? And no matter which copy of the handler I use to access the data, it leads to the same associated data?

---

## Comment 23

> Username: harrishancock  
> Created at: 2017-01-04 04:33:29 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270292966  

That's the hazy idea I have, but like I said, it's a thought-in-progress.  
  
> are you saying that I can associate a handler with data in the io_service? And no matter which copy of the handler I use to access the data, it leads to the same associated data?  
  
Every IO object is backed by an `implementation_type` inside its associated service. This `implementation_type` can be whatever you want. In Asio they're often HANDLEs or file descriptors, but a naive common type to use is a `shared_ptr`. That code I linked to uses an intrusive linked list node as the implementation type, however, which is how it gets around allocating memory without a handler's custom allocation scheme available.  
  
That in itself isn't a solution, but the service would be a logical point at which to place a mutex.  
  
I'm not explaining this well because I'm frankly quite hazy on the idea. I'll work on a proof-of-concept and see where it goes.

---

## Comment 24

> Username: nbougalis  
> Created at: 2017-01-04 04:40:47 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270293532  

This is very interesting! I didn't realize you could do this sort of thing.

---

## Comment 25

> Username: harrishancock  
> Created at: 2017-01-04 04:54:13 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270294650  

> ... no matter which copy of the handler I use to access the data, it leads to the same associated data?  
  
After more thought, I should clarify that I think the answer to this question is explicitly no. IO objects are inherently move-only (or sometimes non-movable), so there is a one-to-one mapping between IO objects and their associated `implementation_type` instances. Copy ctors could be written, but they'd probably only make sense if they created a new `implementation_type` instance, linked to one another like how `handler_ptr` already is implemented. I have a feeling that this solution would end up being equivalent to instantiating a `static std::mutex` member for every `handler_ptr<T>`: i.e., a more-or-less global mutex.

---

## Comment 26

> Username: vinniefalco  
> Created at: 2017-01-04 13:59:34 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270376424  

I'm leaning towards thinking that this is an unsolvable problem. Specifically, it is impossible to write a non-trivial composed operation (i.e. one that requires a non-copyable state) in such a way that all state information is allocated only using the allocator associated with the handler (`asio_handler_allocate`). It becomes tractable if you're willing to admit the use of global mutexes (which I am not).  
  
It is also solvable if you allow a hybrid allocation strategy: design a custom pointer container that allocates the owned object using the allocator associated with the handler, but allocates the control block using plain old `new` and `delete`. This is the strategy I will pursue. This will fix the problem described in the issue and allow users to more easily write composed operations that fulfill the various guarantees. At the expense of a tiny bit of memory coming from the standard heap.  
  
I wish @chriskohlhoff would weigh in on this.

---

## Comment 27

> Username: vinniefalco  
> Created at: 2017-01-04 15:58:44 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270406790  

I believe this is the best we can do:  
https://github.com/vinniefalco/Beast/commits/handler_ptr  
  
Its the previously described hybrid approach.

---

## Comment 28

> Username: harrishancock  
> Created at: 2017-01-04 16:24:45 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270414105  

> I'm leaning towards thinking that this is an unsolvable problem.  
  
I agree.  
  
There is at least hope for the future: the Networking TS only requires Handlers to be MoveConstructible.  
  
I'm grateful for all the hard work you've done exploring this problem! Thank you very much.

---

## Comment 29

> Username: vinniefalco  
> Created at: 2017-01-04 16:35:12 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270417051  

>There is at least hope for the future: the Networking TS only requires Handlers to be MoveConstructible.  
  
Oh...does this solve our problem? It means `use_count` at invocation time would have to be 1...that would be very helpful. And we could store `std::unique_ptr<data>` instead of `std::shared_ptr<data>`.

---

## Comment 30

> Username: harrishancock  
> Created at: 2017-01-04 16:38:32 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270418030  

Yep, it should trivially solve it because we could use `unique_ptr`, as you noted. Something to think about if/when you port Beast to support the Networking TS. I noticed Mr. Kohlhoff already has an implementation based on Asio in his GitHub repos.  
  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/n4625.pdf section 13.2.7.2

---

## Comment 31

> Username: vinniefalco  
> Created at: 2017-01-04 16:51:16 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270421598  

@harrishancock  I would love your thoughts on https://github.com/vinniefalco/Beast/issues/154

---

## Comment 32

> Username: harrishancock  
> Created at: 2017-01-04 16:53:27 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270422207  

Sure. Looks like a long read, might take me a bit!

---

## Comment 33

> Username: vinniefalco  
> Created at: 2017-01-04 16:54:39 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270422537  

Appreciated. If it helps motivate you, this is the last outstanding design problem before Beast is ready for a Boost formal review!!!

---

## Comment 34

> Username: vinniefalco  
> Created at: 2017-01-04 20:16:22 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-270474892  

Feel free to participate in the code review for **1.0.0-b22** which has the fix for this:  
https://github.com/vinniefalco/Beast/pull/218

---

## Comment 35

> Username: vinniefalco  
> Created at: 2017-02-02 12:34:42 UTC  
> Url: https://github.com/boostorg/beast/issues/215#issuecomment-276945929  

Just a heads-up, the API for this class has changed.
