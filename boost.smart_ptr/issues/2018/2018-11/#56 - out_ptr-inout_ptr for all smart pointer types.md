# #56 - out_ptr/inout_ptr for all smart pointer types [Open]

> Username: ThePhD  
> Created at: 2018-11-19 01:17:16 UTC  
> Updated at: 2019-10-14 11:55:36 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56  

Dear Boost.SmartPtr authors:  
  
(This issue is a [continuation of the discussion from Boost.Move](https://github.com/boostorg/move/issues/22).)  
  
My name is JeanHeyd and I am looking to make my [std::out_ptr / std::inout_ptr type that I am proposing to the standard](https://thephd.github.io/vendor/future_cxx/papers/d1132.html) more widely and publicly available. I was given the advice to put it in Boost in some fashion, and I realized that rather than making a new library contribution it would be better off if this was put in one of the 2 smart pointer-containing libraries, Boost.Move or Boost.SmartPtr.  
  
The author of Boost.Move has stated that it seems like a better match for Boost.SmartPtr, and I agree: therefore, I am proposing to add 2 new templated functions and 2 new templated classes: `boost:out_ptr( ... );` and `boost::inout_ptr( ... );` alongside `boost::out_ptr_t<Smart, Pointer, Args...>`  and `boost::inout_ptr_t<Smart, Pointer, Args...>`.  
  
I wanted to know if this was acceptable to the library maintainers here, and if so I would begin to move the current implementation into Boost.  
  
If it is not acceptable, I understand.  
  
If it is acceptable, I would like to know what version of C++ Boost.Smartptr targets and any other caveats I should be careful of when crafting my first pull request. I can do a C++11 implementation without the need for workarounds, but C++03 will require some finesse.  
  
Please do let me know what you think! I eagerly await your response.  
  
Sincerely,  
JeanHeyd

---

## Comment 1

> Username: pdimov  
> Created at: 2018-11-19 02:17:07 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-439751964  

The library in principle supports C++03, but it's fine for independent parts of it to require C++11.  
  
I'm not fond of undefined behavior; what do you think about adding a separate `out_void_ptr`? In addition of avoiding the UB, this also could have the additional benefit of keeping `out_ptr` completely type-safe.

---

## Comment 2

> Username: ThePhD  
> Created at: 2018-11-19 03:49:08 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-439763701  

There is no need for `out_void_ptr`: we can simply have users do `out_ptr<void*>( my_smart_ptr );` to get the same behavior, instead of `out_ptr( my_smart_ptr );`.  
  
The other UB would only apply to `unique_ptr`, and if this gets in here I will talk to the author of Boost.Move directly to have non-UB in the case of `boost::move::unique_ptr` (but there will need to be UB for the fast version of `std::unique_ptr`), unless I want to turn it off.

---

## Comment 3

> Username: pdimov  
> Created at: 2018-11-19 03:54:11 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-439764364  

`out_ptr<void*>` works because of the `static_cast`s, which we could avoid (they break type safety) by having a separate `static_cast`-ing function. `out_void_ptr<X*>` would be a bit odd, but it would work. Other names are possible.  
  
Would you please explain why we need an assignment operator in `out_ptr_t`? The use case is not obvious to me.

---

## Comment 4

> Username: ThePhD  
> Created at: 2018-11-19 04:55:33 UTC  
> Updated at: 2018-11-19 04:57:46 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-439771657  

Most types that have a move constructor have a move assignment operator. While not part of its usage, I was told it was very strange and irregular to have the move constructor without the move assignment operator. Guaranteed copy/move elision means we can technically get away without having one, but for C++11, we're going to need it.  
  
I don't mind having the `out_ptr<void*>` casts myself, because that's a very explicit request on the part of the user ("I want to static cast the internals of this to work with `void*`"). But if it's still too much we can move it into another type / function, sure.

---

## Comment 5

> Username: pdimov  
> Created at: 2018-11-19 13:33:26 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-439893931  

I'm actually not quite sure why we need a move constructor either. :-) Or was that what you had in mind with  
  
> Guaranteed copy/move elision means we can technically get away without having one, but for C++11, we're going to need it.

---

## Comment 6

> Username: pdimov  
> Created at: 2018-11-19 13:53:46 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-439899894  

Another question, why does `out_ptr` initialize the pointer with `.get`?

---

## Comment 7

> Username: pdimov  
> Created at: 2018-11-19 14:01:09 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-439902077  

OK, let's look at the static casts. The second one, in the destructor, enables  
```  
smart_ptr<Derived> p;  
f( out_ptr<Base*>(p) );  
```  
This is not safe, because `f` can put a pointer to `Derived2` into `p`.  
  
The first cast, in the constructor, enables the opposite case:  
```  
smart_ptr<Base> p( new Derived2 );  
f( in_out_ptr<Derived*>(p) );  
```  
This is not safe because `f` assumes it has a valid `Derived*` on entry and it has `Derived2*`.  
  
Note that this second example is safe and would not require a cast if we use `out_ptr` and value-initialize its pointer instead of taking `.get`.  
  
The question, to my mind, is do we want the above to compile? Are the use cases (to the extent there are any) outweighed by the possibility of errors, such as passing the wrong `p` by mistake?

---

## Comment 8

> Username: ThePhD  
> Created at: 2018-11-19 19:30:19 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440013712  

> I'm actually not quite sure why we need a move constructor either. :-) Or was that what you had in mind with  
> > Guaranteed copy/move elision means we can technically get away without having one, but for C++11, we're going to need it.   
  
Post-C++11, Guaranteed Copy/Move Elision was voted into the standard as essentially a strengthening of Return Value Optimization. That means you can return a value that is neither copyable or movable from a function, so long as you can guarantee that RVO will be performed as defined by the language. In C++11 and earlier, even if RVO would kick in, you are not allowed to return an object that is neither copyable or movable.  
  
Even if the compiler never actually invokes either by as-if rules, it was still required to be there. Since C++11 is the target for Boost.Smartptr, we need the move constructor. The move assignment operator just came along for the ride so that the type itself is not "Too Weird". I also was doing tests and found that sometimes a move operation was being called that subtly did the wrong thing, so I needed to define one to be safe.  
  
> Another question, why does out_ptr initialize the pointer with .get?  
  
That's a mistake! Thanks for catching it in the wording: it came from an older implementation I was working on that didn't need the `if (p != nullptr)` check in the destructor. The implementation I have doesn't do this and probably initializes the pointer.  
  
> OK, let's look at the static casts. The second one, in the destructor, enables  
> ...  
> This is not safe, because f can put a pointer to Derived2 into p.  
  
Right, but there are functions in COM (`IUnknown*` and `void*` interfaces, some included in the paper) and other C APIs where this kind of type erasure happens all the time. While not strictly safe here, the idea is that the user made their request for such very explicit (by specifically adding `<Base*>` to the call). It would significantly decrease usability with COM-style APIs if we do not allow casting any kind.  
  
> The first cast, in the constructor, enables the opposite case:  
> ...  
> This is not safe because f assumes it has a valid Derived* on entry and it has Derived2*.  
  
Again, the user has opted into this behavior by adding the template parameter. Default usage is just `out_ptr( p )`: I can only assume the user very explicitly intended for this to happen. If that isn't enough impediment and we want to require the user to do the static casting, or if we want to make it a more uglified template or function parameter (or different call altogether), that could also be a route. I haven't footgun'd myself and neither have the people using my library to date, but their use cases have always been driven by working with the C APIs at minor levels so maybe we've just been lucky in that it's never happened yet.

---

## Comment 9

> Username: pdimov  
> Created at: 2018-11-19 19:56:16 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440021830  

> In C++11 and earlier, even if RVO would kick in, you are not allowed to return an object that is neither copyable or movable.  
  
Yes. Your proposal can get in C++23 at the earliest though, and elision is guaranteed there. So the specification doesn't need to have a move constructor. And if there's no move constructor, no need to have move assignment either. In the C++11 implementation we'll have a move constructor out of necessity, but we don't need the move assignment as nothing requires it.  
  
Regrading `.get`, I suspect that you do this for a reason - you want to enable the optimization of operating directly on the pointer in the `unique_ptr`, and that pointer is already initialized with `.get`, so to speak.  
  
How important is this optimization for you? Would it be acceptable if the Boost implementation doesn't have it?

---

## Comment 10

> Username: glenfe  
> Created at: 2018-11-19 20:09:02 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440025550  

> Regrading .get, I suspect that you do this for a reason - you want to enable the optimization of operating directly on the pointer in the unique_ptr, and that pointer is already initialized with .get, so to speak.  
  
At dinner, JeanHeyd mentioned he needed access to a `unique_ptr` implementation to deliver on performance, and I mentioned that Boost can provide this to him via Boost.Move.  
  
@ThePhD What is the plan for the `unique_ptr` side of things? i.e. Do you (or Ion) know what the hooks for this in Boost.Move's `unique_ptr` implementation will entail?  
  
Also, so that I haven't misunderstood, no such hooks are needed in a `shared_ptr` implementation, correct?

---

## Comment 11

> Username: ThePhD  
> Created at: 2018-11-19 20:25:57 UTC  
> Updated at: 2018-11-19 20:26:49 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440030209  

> Yes. Your proposal can get in C++23 at the earliest though, and elision is guaranteed there. So the specification doesn't need to have a move constructor.   
  
I originally had no move constructor or assignment constructor specified. The person who helped me write the wording made it very explicit that I need to define whether the type is movable, copyable, etc. If not, then I should delete all of the constructors / assignment operators, but I haven't gotten that design decision looked at by anyone just yet.  
  
I also noticed in tests on some compilers that the Move Constructor / Move Assignment was being invoked anyways (and they just did memberwise relocation, which is wrong for these types). After that I decided it was better to define it and not have any dangling questions about how it might work (and RVO can still not use it).  
  
> Regrading .get, I suspect that you do this for a reason - you want to enable the optimization of operating directly on the pointer in the unique_ptr, and that pointer is already initialized with .get, so to speak.  
  
I think I could probably just change the wording to say it's set to an unspecified value. That would allow for the `.get()` implementation for `unique_ptr` and the value-initialization implementation to conform?  
  
> What is the plan for the unique_ptr side of things? i.e. Do you (or Ion) know what the hooks for this in Boost.Move's unique_ptr implementation will entail?  
  
`shared_ptr` requires no hooks (there's no way to obtain unique ownership for `inout_ptr` and reseat the pointer without contemplating potential interference, as well as `inout_ptr` just not working for `shared_ptr` in general). I believe `local_shared_ptr` cannot be reseated because it does not have unique ownership (and so will produce an error just like `shared_ptr`). I currently do that in my implementation with `static_assert(meta::is_specialization_of<Smart, shared_ptr>::value, "cannot use inout_ptr with shared_ptr types!");`. `out_ptr` should work just fine with `shared_ptr` (no hooking required).  
  
`unique_ptr`'s hooks are something I will discuss with the Boost.Move maintainer after we get through here. What I suggested was a `friend` class: `friend class boost::out_otr_t<boost::move::unique_ptr<T, D>, pointer>;`. We can always go without it, but companies that need the high performance and currently hack their standard library will have no reason to leave their handmade solutions and move to boost's (even though many of them already are using boost).  
  
In either case, this is part of the design: once it's here, boost::move can decided how it will react to specializing the template (either not at all (no performance gain) or by adding it (performance gain)). I think the only thing we'll need to define is a macro, `BOOST_SMART_PTR_HAS_OUT_PTR` or similar? (I'm not familiar with protocol on these things.)

---

## Comment 12

> Username: ThePhD  
> Created at: 2018-11-19 21:54:46 UTC  
> Updated at: 2018-11-19 22:05:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440055465  

As another side note: the optimization for getting out the value is only really applied to `inout_ptr_t`, and the internal pointer for `inout_ptr_t`  is already initialized with `Smart.release()`.  
  
The optimization for `out_ptr`  saves a few instructions and cycles at best, and is probably okay not to have.  
  
The most important one is for `inout_ptr_t`.

---

## Comment 13

> Username: glenfe  
> Created at: 2018-11-19 22:09:53 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440059785  

> The optimization for out_ptr saves a few instructions and cycles at best, and is probably okay not to have.  
  
In the case of `out_ptr` this means only that avoiding it would have saved on the combined cost of A) value-initialization of a raw pointer, and B) a  `nullptr` check  - correct?

---

## Comment 14

> Username: ThePhD  
> Created at: 2018-11-19 22:50:51 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440070918  

Yes. If it reseats the pointer then all the implementation has to do is `if (old_ptr != nullptr) { my_unique_smart_ptr.get_deleter()( old_ptr ); }`. The new pointer will be reseated directly into the value. This saves some initialization and checking (particularly on the optimizer's part, see the the way the graphs different for a local pointer that's in-scope and has everything inlined versus an "external" smart pointer that gets reset over and over again: [https://github.com/ThePhD/phd/blob/master/docs/quick/out_ptr.md](https://github.com/ThePhD/phd/blob/master/docs/quick/out_ptr.md))

---

## Comment 15

> Username: pdimov  
> Created at: 2018-11-19 22:58:59 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440072959  

That's because the optimizer doesn't know whether the C-like function doesn't access the "external" smart pointer.  
  
Truth be told, I don't like this optimization in any of the cases, and I'd rather not have it.

---

## Comment 16

> Username: pdimov  
> Created at: 2018-11-19 23:04:07 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440074193  

Incidentally, this means that the optimization is non-conforming given the current wording. You can detect if the constructor of `inout_ptr_t` doesn't call `release`.

---

## Comment 17

> Username: ThePhD  
> Created at: 2018-11-20 01:12:41 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440100061  

I think if I shift the wording to have `.release()` called unconditionally in the destructor, then this doesn't become a problem.

---

## Comment 18

> Username: pdimov  
> Created at: 2018-11-20 01:22:21 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440102005  

You also need something like "If `smart` is modified during the lifetime of this `inout_ptr_t` instance, the behavior is undefined."  
  
(The function can `reset` the smart pointer after writing through the `T**`.)

---

## Comment 19

> Username: glenfe  
> Created at: 2018-11-20 01:29:46 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440103386  

> Truth be told, I don't like this optimization in any of the cases, and I'd rather not have it.  
  
For the Boost implementation, I agree. It might be a concern for standardization and why the paper does go to considerable effort to cover performance, because some people believe this is only viable if there is no added cost, but that does not have to be the case for us. @ThePhD, does that sound reasonable?   
  
This would mean that in the Boost documentation we would not be advertising performance of any kind, like the paper does. This feature would only be about convenience and safety (which to me is the main motivation for using it anyway).   
  
i.e. We could not say that using `boost::out_ptr` is no more expensive than any hand-written code that calls the C function and then conditionally resets a smart pointer, but that on the contrary, it might be less efficient.

---

## Comment 20

> Username: pdimov  
> Created at: 2018-11-20 04:42:32 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440136001  

> I think if I shift the wording to have .release() called unconditionally in the destructor, then this doesn't become a problem.  
  
If you mean something like `g3` here: https://godbolt.org/z/vSROdA, then yes, I think this could work. It also results in significantly better codegen. I'm not sure if your benchmarks include it; if not, it might be worth testing.

---

## Comment 21

> Username: pdimov  
> Created at: 2018-11-20 14:55:17 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440301253  

OK, after a few more experiments I'm coming around to the idea that the optimization may be worth it: https://gcc.godbolt.org/z/rxt-nE

---

## Comment 22

> Username: glenfe  
> Created at: 2018-11-20 15:39:42 UTC  
> Updated at: 2018-11-20 17:57:33 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440317854  

Meanwhile, for `shared_ptr`, where no such optimization is possible, the results are: https://gcc.godbolt.org/z/W7OMTT (where Clang fares much worse than GCC). Perhaps not costly enough that it would be unconscionable to advocate use of this facility.  
  
That said, I suspect the interested users are after this mainly for `unique_ptr`, and I  get the impression that standardization approval is also going to be contingent on such an optimization.

---

## Comment 23

> Username: ThePhD  
> Created at: 2018-11-21 14:46:08 UTC  
> Updated at: 2018-11-22 18:41:30 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440687925  

### For Shared  
  
The code may not look great for shared pointer but the actual speed is fine (for this one compiler (VC++), on this one machine (old Intel i7)). The benchmarks benchmark 2 types but that was purely laziness on my part, the underlying implementation for clever / simple is all the same (I can't think of clever ways to avoid extra work for `shared_ptr`). The "manual" is just doing it by hand:  
  
![reset a pre-existing shared pointer over and over again](https://raw.githubusercontent.com/ThePhD/phd/1c49e17ac06e6a2ae91eabdc06875a8d00f30e31/benchmark_results/out_ptr/shared%20reset%20out%20ptr.png)  
  
![set into a local shared pointer, freshly created pointer](https://raw.githubusercontent.com/ThePhD/phd/1c49e17ac06e6a2ae91eabdc06875a8d00f30e31/benchmark_results/out_ptr/shared%20local%20out%20ptr.png)  
  
### For `inout_ptr` with `unique_ptr`  
  
For `unique_ptr` with `inout_ptr`, the simple implementation where we just unilaterially call `.release()` after is actually a speed _loss_ that only holds to about-as-good-as-manual performance characteristics in the best cases:  
  
![reset into a pre-existing unique pointer](https://raw.githubusercontent.com/ThePhD/phd/1c49e17ac06e6a2ae91eabdc06875a8d00f30e31/benchmark_results/out_ptr/reset%20inout%20ptr.png)  
  
![set into a local unique pointer, freshly created pointer](https://raw.githubusercontent.com/ThePhD/phd/1c49e17ac06e6a2ae91eabdc06875a8d00f30e31/benchmark_results/out_ptr/local%20inout%20ptr.png)  
  
This is most likely because the optimizer can't see through the C API calls in my benchmarks (they are turned into hard optimizer-destroying fences by being compiled into a Dynamic Library, preventing inlining and LTO). This means that doing `.get()` first and then `.release()` in the destructor cannot be optimized away in the case where the C function isn't LTO'd or inlined, leaving the compiler to do both operations and thus not having the same performance as the graphs in the paper itself.  
  
I think I would be better off saying that the value of the `Pointer p` is unspecified after construction, and leaving a normative Remark that the implementation may or may not call `.release()`, and that there are no guarantees until the destructor is run about the state of the smart pointer. This would further discourage clever use, and make sure implementer's hands aren't tied.  
  
([All benchmark results are available here.](https://github.com/ThePhD/phd/tree/master/benchmark_results/out_ptr) You can run the CMake build yourself and have graphs: the default is 250 re-runs of a couple million iterations for each test, so it takes a long time to run (let it rip overnight or decrease the number to something smaller, like 25, for faster turnaround at the expense of less accuracy). I haven't finished adding `rtdsc` cycle-counting support to Google Benchmark...)

---

## Comment 24

> Username: glenfe  
> Created at: 2018-11-21 15:16:43 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-440701101  

> The code may not look great for shared pointer but the actual speed is fine (for this one compiler (VC++), on this one machine (old Intel i7)).  
  
The code not looking great might be a deal breaker for some people (those that do not want to incur the extra bloat).  
  
But the actual speed is not fine when I wrote benchmarks for the above. I observed `g1()` and `g2()` performing the same, which is expected (because the code generated for them is identical). However, `g3()` (which uses `out_ptr` and has the more appalling code generated) did perform worse. I tested clang 7 and gcc 8 on Fedora 29 x86_64 on both a modern Core i7 HQ and a Core i7 U.  
  
With `g3()` I originally said GCC is worse than Clang, but it is the other way around, which Peter pointed out to me.   
  
I'll try MSVC as you suggest to compare these functions there too, and get the results into some presentable state to publish.

---

## Comment 25

> Username: ThePhD  
> Created at: 2018-11-22 18:55:37 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/56#issuecomment-441104027  

I updated and ran some more benchmarks to cover the cases if f1, f2 and their usages in g2 and g3. I haven't actually fixed my fake C API to return an error, so I can't do the equivalent of `throw_system_error` yet, so these numbers are based on best-case "I'm not going to throw after the C function is called". (The codegen will probably pessimize a few more cases if I did that, but I have to go and implement the benchmarks for it). Here are the results (MSVC 15.9.2, Intel i7) I found for best-case, nicely-returning functions and other idioms of creating a pointer:  
  
### Creating/Returning a new shared_ptr  
  
![Creating a pointer and using various ways to construct / return it](https://raw.githubusercontent.com/ThePhD/phd/534005a94976cf522f506649e58543ca2bc118fb/benchmark_results/out_ptr/shared%20local%20out%20ptr.png)  
  
The benchmarks covered a few cases. `manual` means it was done by hand. `inline` means there was no function called to return a shared pointer. `rvo` means a function was called, but the construction of the object was done on line of the `return`, making it a prime candidate for RVO. `return` means that the `shared_ptr` was allocated at the top of the function, then the allocation was done, then it was returned. `no_reset` means that no .reset() call was performed and a temporary pointer was made before stuffing that into a `std::shared_ptr`.  
  
Peculiarly, in almost all cases where some call to `.reset()` was performed, the slowdown is noticeable.... _except_ in the case of making a function, allocating at the top, and then using `out_ptr`, then returning. Only then does `out_ptr` get close to the fastest solution. The fastest seems to be not calling a function at all and doing the allocation `inline` while also constructing the `shared_ptr` with this value directly, without calling `.reset(...)`.  
  
I don't know what magic MSVC is doing to make these numbers. [The code is here](https://github.com/ThePhD/phd/blob/534005a94976cf522f506649e58543ca2bc118fb/benchmarks/out_ptr/source/shared_local_out_ptr.cpp#L12). I didn't look at the codegen yet.  
  
### Resetting a pre-existing shared_ptr  
  
![Resetting a shared pointer using various idioms](https://raw.githubusercontent.com/ThePhD/phd/534005a94976cf522f506649e58543ca2bc118fb/benchmark_results/out_ptr/shared%20reset%20out%20ptr.png)  
  
This has expected characteristics: we have to reset a pre-existing pointer. Both solutions clock in with the same standard deviation and the same average, +-.5 nanoseconds.  
  
Also, Happy Thanksgiving for anyone based in the US! 🦃
