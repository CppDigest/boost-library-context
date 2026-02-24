# #367 fix possible crash when running a boost::thread within a boost::thread [Closed]

> Username: elsamuko  
> Created at: 2022-03-20 18:08:42 UTC  
> Updated at: 2022-05-13 22:54:01 UTC  
> Closed at: 2022-05-11 16:30:25 UTC  
> Url: https://github.com/boostorg/thread/pull/367  

When running a boost::thread within a boost::thread, the inner thread can crash when calling join, when it's joined itself.  
See:  
https://github.com/boostorg/thread/issues/366  
https://stackoverflow.com/q/71482828

---

## Comment 1

> Username: elsamuko  
> Created_at: 2022-05-03 11:19:41 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1115985759  

@pdimov ping

---

## Comment 2

> Username: sehe  
> Created_at: 2022-05-03 15:40:22 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116245412  

I'd expect at `join_noexcept` to handle the interruption exceptions instead of blocking interruption.  
  
The problem was that exceptions emanated from a (conceptually) noexcept operation, and therefore from destructors that used it.  
  
It seems that suppressing interruption might suppress that kind of exception, but I'd imagine it could result in deadlocks if a thread was modeled to never return unless interrupted.  
  
@elsamuko Any chance you can check whether this alternative approach removes your problem?

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-05-03 15:49:07 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116254443  

How would these functions handle the interruption exception?

---

## Comment 4

> Username: elsamuko  
> Created_at: 2022-05-03 16:15:28 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116282964  

@sehe Wouldn't this be practically the same, if I disable the interruption locally or catch `boost::thread_interrupted`?

---

## Comment 5

> Username: elsamuko  
> Created_at: 2022-05-03 16:22:31 UTC  
> Updated_at: 2022-05-03 16:47:48 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116290153  

@sehe But it works, too (based on [this sample](https://gist.github.com/elsamuko/6e178c37fa2cf8742cb6bf512f2ff866)):  
  
            // boost::this_thread::disable_interruption di;  
            t.interrupt();  
            try { t.join(); } catch( boost::thread_interrupted& ) {}

---

## Comment 6

> Username: pdimov  
> Created_at: 2022-05-03 16:24:35 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116292353  

It just does the same thing, less efficiently.

---

## Comment 7

> Username: sehe  
> Created_at: 2022-05-03 16:33:00 UTC  
> Updated_at: 2022-05-03 16:35:26 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116300697  

> How would these functions handle the interruption exception?  
  
@pdimov  Again, not sure. Perhaps `boost::thread_interrupted` can be handled by ignoring it. Mmm. When I feared potential deadlock because the inner thread would never join, I forgot that `disable_interruption` applies to the joining thread, not the thread being joined, obviously.  
  
However, `boost::thread::join` is [specified](https://www.boost.org/doc/libs/1_79_0/doc/html/thread/thread_management.html#interruption_points) as an interruption point. Disabling interruption in `join_noexcept` seems to break that?  
  
The more I think about this, the more I wonder whether `join_noexcept` was badly named (specifically meaning it won't check preconditions), and should raise thread_interruption if only to satisfy the interruption_point criteria. (Notice how the overload isn't marked `noexcept` either, independent of language support, while other functions _are_).  
  
And by consequence, the best fix would be to handle (ignore) said exception when in a destructor - such as the `thread_guard` destructor.

---

## Comment 8

> Username: pdimov  
> Created_at: 2022-05-03 16:38:30 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116305983  

Well, a noexcept function can't be an interruption point, obviously. And I can't look up what the docs say because boost.org is broken. :-)

---

## Comment 9

> Username: sehe  
> Created_at: 2022-05-03 16:53:18 UTC  
> Updated_at: 2022-05-03 16:53:45 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116327109  

Hah. My DNS/cache wins. The function *isn't* noexcept.  
  
`join()` **is** an interruption point and its implementation unconditionally calls `join_noexcept()`. Because the only extra checks in `join()` perform precondition checks, it follows that `join_noexcept()` **must** be an interruption point.  
  
Hence my conclusion that (A) `join_noexcept` is unfortunately named (B) we have to plug the hole where it exists: `join()` from a destructor.

---

## Comment 10

> Username: elsamuko  
> Created_at: 2022-05-03 16:59:56 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116333041  

Doc link: https://github.com/boostorg/thread/blob/develop/doc/thread_ref.qbk#L345

---

## Comment 11

> Username: pdimov  
> Created_at: 2022-05-03 17:16:47 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116348175  

`join` is definitely an interruption point; the question was whether `join_noexcept` was intended to be one.

---

## Comment 12

> Username: sehe  
> Created_at: 2022-05-03 18:28:43 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116423048  

I tried to answer that. Were you able to verify my reasoning?

---

## Comment 13

> Username: pdimov  
> Created_at: 2022-05-03 18:38:25 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116432691  

Yes I did. `thread::join` is [here](https://github.com/boostorg/thread/blob/4abafccff4bdeb4b5ac516ff0c2bc7c0dad8bafb/include/boost/thread/detail/thread.hpp#L758-L765) and it throws on deadlock (a thread joining itself) and on precondition violations (thread not joinable). It calls `join_noexcept`, which necessarily implies that `join_noexcept` is an interruption point. This makes this PR invalid, because it removes the interruptibility of `join`.  
  
The fix for the issue must be found elsewhere.

---

## Comment 14

> Username: sehe  
> Created_at: 2022-05-03 20:16:19 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116548705  

> The fix for the issue must be found elsewhere.  
  
I'll draw up a fix adding exception handling to the thread_guard destructor. I can't think of better ways. Of course, I can only make it squelch interruption exceptions, because otherwise we risk leaving programs in invalid states for unknown reasons.

---

## Comment 15

> Username: elsamuko  
> Created_at: 2022-05-03 21:45:12 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116691555  

Another possiblity would be adding the [`non_interruptable_interrupt_and_join_if_joinable`](https://gist.github.com/elsamuko/6e178c37fa2cf8742cb6bf512f2ff866#18) to the list of thread functors.    
If someones is not using a `thread_guard`, then it's still crashing.    
And `join_noexcept` would still throw.

---

## Comment 16

> Username: pdimov  
> Created_at: 2022-05-03 22:14:55 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116712735  

Destructors need `disable_interruption`. Which of our destructors has the problem? `~thread_guard`?

---

## Comment 17

> Username: sehe  
> Created_at: 2022-05-04 00:40:40 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1116832543  

Adding it to the thread functors makes little sense to me, as they're useful outside of destructors.  
  
I PR-ed the alternative approach here: https://github.com/boostorg/thread/pull/369

---

## Comment 18

> Username: elsamuko  
> Created_at: 2022-05-09 16:15:43 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1121304401  

I summarized all testcases so far here: https://github.com/elsamuko/boost_thread_testcases  
  
* fixing `thread_guard` alone will not solve all cases, `scope_guard` and `scoped_thread` will still crash  
* adding the `disable_interruption` in `join_noexcept` fixes all cases, but makes `join()` non-interruptable  
* adding the `disable_interruption` before any join to the `thread_functors` fixes all but the custom `scope_guard`  
* same with using the `non_interruptable_interrupt_and_join_if_joinable` custom functor  
  
Imho the best solution woult be making the `join_if_joinable` and `interrupt_and_join_if_joinable` functors non-interruptable.

---

## Comment 19

> Username: sehe  
> Created_at: 2022-05-09 16:33:25 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1121324281  

Nice work.  
  
I feel your "best solution" is the easiest fix.  
  
Sadly it is also a breaking change, which will only be feasible if we can either  
 - prove that no existing users of Boost Thread could ever usefully rely on the old behaviour  
 - we build broad consensus that breaking the behavior is the best solution.  
  
Am I reading correctly that your 3rd bullet  "adding the disable_interruption before any join to the thread_functors fixes all but the custom scope_guard" basically means that if people want to shoot themselves in the foot, they are free to do so? In that case, maybe this fix is actually best.  
  
Perhaps we can add warning about `join` potentially throwing interruption exceptions at various points in the documentation.

---

## Comment 20

> Username: elsamuko  
> Created_at: 2022-05-10 15:51:30 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1122575589  

I added non-interruptible versions of the joining thread functors (`non_interruptable_join_if_joinable`, `non_interruptable_interrupt_and_join_if_joinable`).    
Then it's not a breaking change.

---

## Comment 21

> Username: sehe  
> Created_at: 2022-05-10 16:00:19 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1122589565  

I'm confused. @elsamuko Throwing from a destructor is never ok. Therefore there is no possible use of throwing functors with thread_guard, meaning that the blanket fix in https://github.com/boostorg/thread/pull/369 is strictly better. What am I missing?

---

## Comment 22

> Username: elsamuko  
> Created_at: 2022-05-10 16:05:57 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1122595666  

@sehe It's throwing only, when it's within a `boost::thread`, which should be a edge case.   
  
#369 is missing a fix for `boost::scoped_thread`. And I'm not sure, if it's guaranteed to be a non-breaking change.

---

## Comment 23

> Username: sehe  
> Created_at: 2022-05-10 16:17:05 UTC  
> Updated_at: 2022-05-10 16:17:27 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1122607000  

> @sehe It's throwing only, when it's within a boost::thread  
  
Yes? The point is destructors can't throw  
  
> https://github.com/boostorg/thread/pull/369 is missing a fix for boost::scoped_thread.   
  
I noted that https://github.com/boostorg/thread/pull/369#issuecomment-1122590610. That's just a different thing  
  
> And I'm not sure, if it's guaranteed to be a non-breaking change.  
  
It's "breaking" in the sense that applications that were guaranteed to abnormally terminate, might in rare cases now hang because the destructor uses a join that doesn't get interrupted.  
  
Not a reason to introduce half-baked fixes with ridiculous names that nobody will know to use when they needed to, IMO

---

## Comment 24

> Username: pdimov  
> Created_at: 2022-05-10 16:49:48 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1122639600  

I agree that disabling interruptions in the destructors is the correct change. See also my comments in #369.

---

## Comment 25

> Username: elsamuko  
> Created_at: 2022-05-10 17:24:25 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1122670968  

> It's "breaking" in the sense that applications that were guaranteed to abnormally terminate, might in rare cases now hang because the destructor uses a join that doesn't get interrupted.  
  
Do you have an example, where the interruptability of `join()` would lead to a deadlock if removed? I don't understand why I would want to interrupt a thread, which is joining another thread. This looks more like an unexpected side effect of using `boost::condition_variable::wait()` in `join_noexcept()`.  
  
Removing the interruptability of `join()` would fix crashes in custom scope guards and destructors, too.

---

## Comment 26

> Username: pdimov  
> Created_at: 2022-05-10 17:38:57 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1122685734  

The general principle is that all blocking functions are interruptible. `join` is a blocking function, so it's interruptible.  
  
All three of the examples in #369 show cases where an outer thread that owns and is joining an inner thread is interrupted. This is a typical use scenario; an outer thread spawns a group of scoped inner threads, passing them pieces of the task to be performed, then joins them at the end. The user of the outer thread interrupts the outer thread, and the outer thread is responsible for interrupting the inner worker threads.

---

## Comment 27

> Username: pdimov  
> Created_at: 2022-05-10 17:48:34 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1122694102  

E.g. consider something like this  
```  
static void outer_thread_func()  
{  
    boost::scoped_thread<boost::interrupt_and_join_if_joinable> inner1( do_first_half );  
    boost::scoped_thread<boost::interrupt_and_join_if_joinable> inner2( do_second_half );  
  
    inner1.join();  
    inner2.join();  
}  
```  
This thread owns two worker subthreads, assigns them one half of the work to be done, and waits for them to finish. If the outer thread is interrupted, one of the `join`s throws, and the `scoped_thread`s automatically interrupt+join the worker subthreads.

---

## Comment 28

> Username: elsamuko  
> Created_at: 2022-05-11 16:30:25 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1123994830  

Thanks for the explanation, then a non-interruptible `join()` would break existing software.

---

## Comment 29

> Username: pdimov  
> Created_at: 2022-05-13 22:54:01 UTC  
> Url: https://github.com/boostorg/thread/pull/367#issuecomment-1126562288  

Should be fixed with https://github.com/boostorg/thread/commit/f71e0f1645413c3291e37d766e5b04bc57c6e132 and https://github.com/boostorg/thread/commit/8db325363b8e91de23c062ec8964bb605ad89f11.

---
