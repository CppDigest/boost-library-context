# #211 Thread contention with GMP backend [Closed]

> Username: wlandry  
> Created at: 2020-04-01 00:23:55 UTC  
> Updated at: 2020-05-13 04:41:41 UTC  
> Closed at: 2020-04-05 12:52:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211  

I compile the file [gmp_threads.cxx](https://github.com/boostorg/multiprecision/files/4411920/gmp_threads.txt) with the command  
  
`g++ gmp_threads.cxx -o gmp_threads -lgmp -lpthread -Iqft/src/boost_bin/include -g`  
  
and run it under valgrind/helgrind with the command  
  
`valgrind --tool=helgrind ./gmp_threads`  
  
which gives the [these warnings](https://github.com/boostorg/multiprecision/files/4411958/gmp_threads_valgrind.txt).  Looking into the details, it seems that boost::multiprecision is setting the precision in `boost::multiprecision::detail::scoped_default_precision` on line 123 of  `precision.hpp`.  I think it hands this off to gmp's internal routine to set precision.  I think it is properly guarded by a mutex, so there is no obvious undefined behavior.  However, locking this mutex causes other threads to block, which causes my multithreaded program running on 32 cores to run about as fast as a single core.  
  
I do not see this problem with Boost 1.68, but I do see it with 1.70 and later (including the current version on github).  
  
This PR checks within the constructors and destructors of `scoped_default_precision` for whether the precision is actually changing.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2020-04-01 07:30:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607083216  

> whether the precision is actually changing  
  
When I first read the headlines of this post, my gut feeling was, ...  
only change the precision if it is actually being changed.  
  
... Which is exactly what the PR suggests to do.  
  
Did the changes in your PR restore the 32 core performance you expected?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-04-01 16:07:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607341451  

Ugh, this opens up a whole can of worms that we need to fix.  
  
1) Yes we should only set the precision when it's changed.  
2) We should be using atomic operations for this.  
3) It's very temping to declare the default precision thread local, but if we do that child threads will no longer see the same default precision as their parent, and that could break a whole lot of code.  
4) However, if we *don't* make the default precision thread-local, then different threads operating at different precisions will definitely interfere with one another with unpredictable results.  
  
Thoughts?

---

## Comment 3

> Username: pabristow  
> Created_at: 2020-04-01 16:22:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607350328  

Quick - close the can!

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2020-04-01 17:12:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607377597  

I'm perplexed by this as I'm not seeing the contention - rather the opposite (the race condition which clearly needs fixing).  BTW we do *not* ever change the default default precision of the underlying GMP library.  Do you have a clear idea of where the contention is?

---

## Comment 5

> Username: wlandry  
> Created_at: 2020-04-01 17:21:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607382231  

> Did the changes in your PR restore the 32 core performance you expected?  
  
Yes.

---

## Comment 6

> Username: wlandry  
> Created_at: 2020-04-01 17:40:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607392253  

> I'm perplexed by this as I'm not seeing the contention - rather the opposite (the race condition which clearly needs fixing). BTW we do _not_ ever change the default default precision of the underlying GMP library. Do you have a clear idea of where the contention is?  
  
I was mistaken.  The precision is stored in a `static` in `gmp_float_imp`.  I would guess that gcc is adding some mutexes to make sure that that we do not shoot ourselves in the foot when reading and updating it.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2020-04-01 18:03:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607405112  

>> Thoughts?  
  
> I'm perplexed by this as I'm not seeing the contention  
  
It might be relatively straightforward. But I am not sure.  
  
I have a few preliminary, non-confirmed thoughts.  
  
Each GMP object has a backend struct (a classical C-style struct) that manages its memory pointer and various other characteristics of the GMP number. In Boost.Multiprecision, we wrap this struct. The low-level precision-changing function in GMP, is already atomic and secured by its own mutexes.  
  
So it seems like 1 and 2 in the can of worms reduce to thread-safe handling of `m_old_prec` and `m_new_prec`. Since most guys are running 32-bit or 64-bit architectures, however, modificatioins of these `int` members are pretty much atomic by the architecture of the CPU, due to the fact that they can be read, written, read-write-exchanged in single CPU cycles.  
  
3 and 4 might fall into the region of handling a static default precision which may or may not need to be changed after static initialization (i.e. the static initialization that runs prior to calling the `main()` subroutine),  
  
So the original PR might just be enough.  
  
I will give this considerable more thought.  
  
Kind regards, Chris

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2020-04-01 18:24:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607415686  

>The low-level precision-changing function in GMP, is already atomic and secured by its own mutexes.  
  
We don't call that though.  We hold our own default precision which is quite separate from GMP's.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2020-04-01 18:59:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607433043  

I think we need something more like: https://github.com/boostorg/multiprecision/pull/212  
  
@wlandry can you give this a whirl?

---

## Comment 10

> Username: wlandry  
> Created_at: 2020-04-01 19:30:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607447339  

> I think we need something more like: #212  
>   
> @wlandry can you give this a whirl?  
  
This should work for my case.  It makes it explicit that reads and writes are atomic.  I think it ends up being identical to my changes for the x86_64 platforms I run on, but I could see it making a difference on other platforms.  
  
However, it still uses a global variable, which is kind of a disaster for multithreaded use.  If thread `A` atomically sets the precision to 100, thread `B` can atomically set it to 200.  Then all of the thread `A`s computations will run at precision=200, not 100.  Fixing this requires making the precision non-static.  But then you would not need to make it atomic either.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2020-04-01 19:44:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607453930  

>However, it still uses a global variable, which is kind of a disaster for multithreaded use.   
  
I agree, the issue then is that if code sets the default precision at the start of `main()`, and then spawns a thread or 2, those threads *do not* inherit the same default precision as the main thread.  This is IMO a very important use case?

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2020-04-01 20:36:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607476860  

> I think we need something more like: #212   
  
I'm not sure, and maybe I am missing something simple... But that seems like a lot of effort to make read/write operations to values of type `unsigned` atomic. Maybe I'm missing something, but read/write/modify operations on `unsigned` are almost always atomic unless you are dealing with something like an 8-bit architecture and unsigned is 16 bits or something like that. Then you will need multiple cycles to read/write/modify `unsigned`. But I think most all platforms using multiprecision can do single-cycle read/write/modify of `unsigned`. But I guess just to be sure, they can be atomics.  
  
The issues regarding the static precision returned as a reference do, in fact, seem like they need to be more fully discussed.  
  
>> The low-level precision-changing function in GMP, is already atomic and secured by its own mutexes.  
  
> We don't call that though. We hold our own default precision which is quite separate from GMP's.  
  
Somewhere we must be calling the low-level functions that actually do the memory allocation/deallocation in the depths of GMP. It is these that should already be secured with their own mutexes, those memory accesses. I was not referring to the class-local members of type `unsigned` that can be modified.  
  
Kind regards, Chris

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2020-04-01 20:43:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607480136  

>>> The low-level precision-changing function in GMP, is already atomic and secured by its own mutexes.  
  
>> We don't call that though. We hold our own default precision which is quite separate from GMP's.  
  
> Somewhere we must be calling the low-level functions that actually do the memory allocation/deallocation in the depths of GMP.  
  
My text was vague. I mean subroutines such as `mpf_set_prec_raw()` should already be thread safe in gmp_threads.

---

## Comment 14

> Username: wlandry  
> Created_at: 2020-04-01 21:17:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607494228  

> I agree, the issue then is that if code sets the default precision at the start of `main()`, and then spawns a thread or 2, those threads _do not_ inherit the same default precision as the main thread. This is IMO a very important use case?  
  
I think that the current situation, where thread `A` can interfere with thread `B`, is not acceptable.  
Thinking some more, maybe store the default precision in thread local storage?  If it is not set, then it can read the global value once.  As I understand it, thread local storage is a problem on some architectures, but this might be the best way forward.  
  
With all that said, this still does not cover coroutines.  They do not have their own copies of statics, so they can create similar kinds of trouble.  This kind of argues that all numbers should get their precision either explicitly from constructor arguments or implicitly from other high precision numbers.  That would be a big change.  
  
The root of the problem seems to be that boost::multiprecision is setting and resetting the default precision. Does it really have to do that?

---

## Comment 15

> Username: ckormanyos  
> Created_at: 2020-04-02 05:02:35 UTC  
> Updated_at: 2020-04-02 05:03:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607622440  

> The root of the problem seems to be that boost::multiprecision is setting and resetting the default precision. Does it really have to do that?  
  
I don't know.  
  
There are 3 `mpf_...`precision functions, `mpf_set_default_prec`, `mpf_set_prec` and `mpf_set_prec_raw`.  
  
There is also the [The GNU Multiple Precision Arithmetic Library](https://gmplib.org/gmp-man-6.2.0.pdf), which very tersely describes the functionality of each of these three near the end of Chapter 7.1.  
  
But it is difficult because you sometimes need to reallocate, while at other times you only need to change the working precision for a calculation. To repeat the comments of John, ... _Ugh_.  
  
I could imagine a pretty straightforward thread-safe design that uses `mpf_set_prec` for construction (`mpf_set_prec` is the one that can allocate/realocate), and subsequently uses `mpf_set_default_prec` combined with its mirror member variable(s) `m_prec_old`, `m_prec_new` for temporatily increasing or decreasing precision on a calculational basis. In this design, the _root_ precision (the one given by `mpf_get_prec`) would be `constexpr` and invariant.  
  
But I believe Multiprecision's object oriented design allows for multiple copies of the _root_ precision (the one given by `mpf_get_prec`) because you can construct from _other_ `mpf_t` that was allocated with a different default allocation size.  
  
John will know a lot more about the internal details of this case than I do. But it might, indeed, be the case that not each and every issue regarding mixed-precision has been optimally handled.  
  
Kind regards, Chris

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2020-04-02 16:23:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-607948865  

Just to clarify how things work currently:  
  
We never mess with GMP's default precision, the precision of each object is set in it's constructor when we call `mpf_init2` - ie initialize to a specific precision in one step rather than a 2 step, initialize and then change the precision.  
  
It is also possible to set the precision of a specific mpf_float object at construction time, by using it's 2 arg constructor:  
  
```  
   boost::multiprecision::mpf_float f("1.23", 30);  
```  
  
The problem with this, and the reason for the scoped-precision objects controlling the default, is with existing generic code which relies on default constructors, indeed this will be the case for any generic code, given that the 2-arg construction is very Boost.Multiprecision specific.  
  
None the less this does allow for some interesting mixed-precision algorithms where variables have just as much precision as they need, and expressions containing objects of differing precisions are evaluated at the highest precision of them all.  
  
That means that all the precisions in play are stored locally within Boost.Multiprecision and we can therefore implement any reasonably sensible scheme for the multi threaded case (I hope!).  
  
Thinking out loud here, the only alternative scheme I can think of is a 3 tier one in place of the current 2 tier:  
  
* The precision of each object.  
* The default precision for the current thread: defaults to the global default precision at the time this default is first needed.  This is *not* the same thing as the global default at the time the thread is created, or the the thread local default of the thread which spawned this one.  
* The global default.  
  
Existing code which sets the global default in main() before any threads are created behaves as before.  As do single-threaded algorithms which use multiple precisions.  New code would be encouraged to always change the current thread-local precision and pass the requested precision in the call stack to new threads so they can also set their local default.  The internal scoped-precision objects would only need to deal with the thread-local precision so there would be no contention: not even an atomic load/store required.  
  
Thoughts?  
  
BTW I tried quite hard to discover the cause of the contention and couldn't find any at least with gcc-9.  I believe some early C++11 implementations used a single global mutex to check whether a function-scope-static had been constructed already or not, but unless you're using a moderately ancient GCC version you really shouldn't see that.

---

## Comment 17

> Username: wlandry  
> Created_at: 2020-04-02 18:53:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-608042105  

> The problem with this, and the reason for the scoped-precision objects controlling the default, is with existing generic code which relies on default constructors, indeed this will be the case for any generic code, given that the 2-arg construction is very Boost.Multiprecision specific.  
  
Would it work if objects created from single argument constructors got their precision from the argument?  If you pass an int64_t, you get a 64 bit mantissa.  If you pass a double, you get a 53 bit mantissa.  I could still imagine some problematic expressions, like `1.0 + 1e-100`, which give different answers depending on precision.  Is that too much of a breaking change?  
  
> Thinking out loud here, the only alternative scheme I can think of is a 3 tier one in place of the current 2 tier:  
>   
>     * The precision of each object.  
>   
>     * The default precision for the current thread: defaults to the global default precision at the time this default is first needed.  This is _not_ the same thing as the global default at the time the thread is created, or the the thread local default of the thread which spawned this one.  
>   
>     * The global default.  
>   
>   
> Existing code which sets the global default in main() before any threads are created behaves as before. As do single-threaded algorithms which use multiple precisions. New code would be encouraged to always change the current thread-local precision and pass the requested precision in the call stack to new threads so they can also set their local default. The internal scoped-precision objects would only need to deal with the thread-local precision so there would be no contention: not even an atomic load/store required.  
>   
> Thoughts?  
  
This solution will still cause coroutines to have action-at-a-distance behavior.  
  
> BTW I tried quite hard to discover the cause of the contention and couldn't find any at least with gcc-9. I believe some early C++11 implementations used a single global mutex to check whether a function-scope-static had been constructed already or not, but unless you're using a moderately ancient GCC version you really shouldn't see that.  
  
I saw this contention with gcc 8.3.0 on Debian 10, gcc 7.3.0 on Centos 6.6, gcc 6.4.0 on RHEL 7.7, gcc 8.3.0 on Suse 15, and gcc 8.3.0 on RHEL 7.6.  It seems pretty generic.

---

## Comment 18

> Username: wlandry  
> Created_at: 2020-04-03 00:32:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-608165610  

> > BTW I tried quite hard to discover the cause of the contention and couldn't find any at least with gcc-9. I believe some early C++11 implementations used a single global mutex to check whether a function-scope-static had been constructed already or not, but unless you're using a moderately ancient GCC version you really shouldn't see that.  
>   
> I saw this contention with gcc 8.3.0 on Debian 10, gcc 7.3.0 on Centos 6.6, gcc 6.4.0 on RHEL 7.7, gcc 8.3.0 on Suse 15, and gcc 8.3.0 on RHEL 7.6. It seems pretty generic.  
  
It is possible that the performance problem that I am seeing is not from mutexes, but cache invalidation.  The CPU's have to reload the precision each time it gets written by anyone.  With multiple CPU's on different sockets, this means going all the way back to main memory, which is painfully slow.

---

## Comment 19

> Username: ckormanyos  
> Created_at: 2020-04-03 07:30:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-608277442  

> Thinking out loud here, the only alternative scheme I can think of is a 3 tier one in place of the current 2 tier:  
  
I need to dive more deeply into the code design in order to comment reliably on the depth of this approach. Please give me a bit of time for that.

---

## Comment 20

> Username: ckormanyos  
> Created_at: 2020-04-03 07:38:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-608280478  

> It is possible that the performance problem that I am seeing is not from mutexes, but cache invalidation.  
  
In my experience, the much more common and (for runtime of significant influence) is more often thread traffic. This involves dispatching threads, stopping or blocking threads, or worst case creating threads. Since you are sort of at the whim of gmp_threads, that is sort of a big unknown.  
  
In order to shed some light on this, I would do things like...  
  
- Run with one single thread with native GMP  
- Run with 2 or 4 threads both with and without your simple fix in this PR.  
- Run with significantly more digits, such as twice as many or even ten-fold.  
  
When doing all these things, you will potentially see a pattern. Does it look like thread traffic, or does it look like a memory barrier effect?  
  
How many digits are you using in the first place?  
Are you even changing precision? Or are you just getting traffic at the subroutine that _might_ change precision?  
  
Answering all these questions will greatly help with  our analyses.  
  
As a final point, and this is not really related to the original PR, but relevant nonetheless... I almost never recommend the multithreaded granularity on the big-number level (i.e., within the GMP lib). The reason for this is that thread overhead is likely your first and worst offer with that extremely low-level threading granularity. It is moch more efficient in general to identify entire rows of calculations or possible ways for higher-level parallelization. Think like the rows of a matrix, or an entire line of a loop within a calculation. It is with this higher-level granularity that you will minimize threading traffic and overhead and maximize algorithmic output. Again, this has not much to do with the original PR, but check out the possibilities in your desing.  
  
kind regards, Chris

---

## Comment 21

> Username: wlandry  
> Created_at: 2020-04-03 21:51:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-608704315  

> > It is possible that the performance problem that I am seeing is not from mutexes, but cache invalidation.  
>   
> In my experience, the much more common and (for runtime of significant influence) is more often thread traffic. This involves dispatching threads, stopping or blocking threads, or worst case creating threads. Since you are sort of at the whim of gmp_threads, that is sort of a big unknown.  
  
I do not know what you mean by gmp_threads.  GMP is not creating any threads.  The application creates and destroys threads only a few times.  When it does create threads, it only creates a small number of them (it is a run time parameter, generally set to the number of cores). Most of the work is independent and done within the threads.  
  
If you are curious, the application is [scalar_blocks](https://gitlab.com/bootstrapcollaboration/scalar_blocks).  
  
> In order to shed some light on this, I would do things like...  
>   
>     * Run with one single thread with native GMP  
>   
>     * Run with 2 or 4 threads both with and without your simple fix in this PR.  
>   
>     * Run with significantly more digits, such as twice as many or even ten-fold.  
>   
>   
> When doing all these things, you will potentially see a pattern. Does it look like thread traffic, or does it look like a memory barrier effect?  
  
Here are two tables for a medium sized-benchmark.  The output is from /usr/bin/time.  
  
Without fix  
```  
| Threads | Precision (bits) | User (s) | System (s) | Elapsed (s) | CPU % |  
|       1 |              665 |    52.33 |       0.13 |       52.54 |    99 |  
|       2 |              665 |    81.02 |       0.16 |       42.12 |   192 |  
|       4 |              665 |   133.00 |       0.21 |       35.41 |   376 |  
|       8 |              665 |   284.18 |       0.18 |       37.06 |   767 |  
|      16 |              665 |   497.90 |       0.24 |       33.11 |  1504 |  
|      28 |              665 |   775.97 |       0.38 |       30.99 |  2504 |  
|       1 |             1230 |   101.78 |       0.43 |      102.36 |    99 |  
|       2 |             1230 |   121.39 |       0.40 |       62.12 |   196 |  
|       4 |             1230 |   157.51 |       0.36 |       41.23 |   382 |  
|       8 |             1230 |   263.51 |       0.36 |       35.52 |   742 |  
|      16 |             1230 |   584.73 |       0.41 |       39.15 |  1494 |  
|      28 |             1230 |   790.33 |       0.54 |       31.64 |  2499 |  
```  
With fix  
```  
| Threads | Precision (bits) | User (s) | System (s) | Elapsed (s) | CPU % |  
|       1 |              665 |    53.07 |       0.17 |       53.45 |    99 |  
|       2 |              665 |    52.69 |       0.24 |       27.00 |   195 |  
|       4 |              665 |    52.69 |       0.18 |       13.62 |   388 |  
|       8 |              665 |    52.71 |       0.18 |       07.25 |   729 |  
|      16 |              665 |    52.84 |       0.18 |       04.11 |  1288 |  
|      28 |              665 |    53.00 |       0.22 |       02.28 |  2327 |  
|       1 |             1230 |   102.13 |       0.25 |      102.48 |    99 |  
|       2 |             1230 |   122.15 |       0.31 |       62.44 |   196 |  
|       4 |             1230 |   138.33 |       0.28 |       35.93 |   385 |  
|       8 |             1230 |   177.68 |       0.36 |       24.13 |   737 |  
|      16 |             1230 |   377.57 |       0.33 |       25.78 |  1465 |  
|      28 |             1230 |   532.65 |       0.49 |       21.69 |  2457 |  
```  
  
The most dramatic difference is between the `Elapsed` time with the smaller precision.  That difference is what caused me to look into this.  For large precision, I would guess that the memory allocator is the bottleneck.  The application creates lots of GMP temporaries.  But that is for another day.  
  
> How many digits are you using in the first place?  
  
665 bits, or 200 decimal digits.  
  
> Are you even changing precision? Or are you just getting traffic at the subroutine that _might_ change precision?  
  
I never change precision.  
  
> As a final point, and this is not really related to the original PR, but relevant nonetheless... I almost never recommend the multithreaded granularity on the big-number level (i.e., within the GMP lib). The reason for this is that thread overhead is likely your first and worst offer with that extremely low-level threading granularity. It is moch more efficient in general to identify entire rows of calculations or possible ways for higher-level parallelization. Think like the rows of a matrix, or an entire line of a loop within a calculation. It is with this higher-level granularity that you will minimize threading traffic and overhead and maximize algorithmic output. Again, this has not much to do with the original PR, but check out the possibilities in your desing.  
  
That is what I already do.  This is not an application that creates a bajillion threads and lets the operating system sort it out.

---

## Comment 22

> Username: ckormanyos  
> Created_at: 2020-04-04 07:53:35 UTC  
> Updated_at: 2020-04-04 07:55:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-608992047  

>>> It is possible that the performance problem that I am seeing is not from mutexes, but cache invalidation.  
  
>> In my experience, the much more common and (for runtime of significant influence) is more often thread traffic. This involves dispatching threads, stopping or blocking threads, or worst case creating threads. Since you are sort of at the whim of gmp_threads, that is sort of a big unknown.  
  
>> I do not know what you mean by gmp_threads. GMP is not creating any threads.  
  
I was wrong and mistaken. Thank you for clearing that up.  
  
> Here are two tables for a medium sized-benchmark. The output is from /usr/bin/time.  
  
Thank you Walter for creating this detailed and very clear information. For me at a distance, and also based on your observation, it seems like the runtime influence of some kind of thread traffic is creating a bottleneck. Why? This is because especially at low digit counts the percentage of the influence is stronger, possibly indicating that there is either thread contention or as you mention allocation/deallocation happening on a roughly per-bignumber-basis. When the work required for (already) happening multiplication/division, etc. of a single bignumber increases, the threading traffic or allocation/deallocation decreases in its ratio with the real work needing to be done.  
  
> For large precision, I would guess that the memory allocator is the bottleneck.  
  
I agree. But it could also be a thread lock/unlock mechanism. I do not believe that threads are being created and destroyed. I almost chuckled a little bit yesterday because before I knew your number of digits, I wanted to mention that in my experience thread lock/unlock can have the runtime amount roughly equivalient to elementary operations on something like a 100 decimal digit bignumber. Somehow, this crazy rule of thumb can also be seen in your benchmarks.  
  
I guess I have another two questions:  
  
@wlandry Do you get the proper numerical results with/without your fix?  
  
@jzmaddock Do you think the original PR is a good step in the right direction? Would the results be better in general for multiprecision? I wonder if we really need to deal with all four points you mention in the "can of worms", or if the original PR that pretty much deals with points 1 and 2 is a step in the right direction, and leave further refinements for the future?  
  
Kind regards, Chris

---

## Comment 23

> Username: jzmaddock  
> Created_at: 2020-04-04 15:56:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-609049108  

> For large precision, I would guess that the memory allocator is the bottleneck.  
  
Actually I doubt that - for higher precisions the cost of memory allocations is usually trivial compared to the cost of performing the arithmetic.  
  
> Do you think the original PR is a good step in the right direction?  
  
Yes but we really do need to make the global-default-precision atomic in order to be correct (ie free from data-tearing).  That's my bad.  
  
> leave further refinements for the future?  
  
Yes it's too late for design changes for the next release anyway, I will try and get some sort of fix in before the beta deadline if the tests will cycle in time!  
  
> This solution will still cause coroutines to have action-at-a-distance behavior.  
  
I don't see any solution for coroutines at the current time - except that, since the location of the yield is always known, coroutine can (and should) save and restore global state at each yield point - this doesn't just effect Multiprecision, but coroutines that need to mess with the FPU flags (for example) as well.

---

## Comment 24

> Username: wlandry  
> Created_at: 2020-04-04 17:12:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-609060029  

> > For large precision, I would guess that the memory allocator is the bottleneck.  
>   
> Actually I doubt that - for higher precisions the cost of memory allocations is usually trivial compared to the cost of performing the arithmetic.  
  
That has not been my experience.  I have spent a fair amount of time looking at perf data for a similar application.  It is not that hard to create lots of temporaries in tight loops.  If everything is on the stack, this is not a problem, but GMP creates everything on the heap.  Getting rid of all of these temporaries is a bit of a pain.  You have to explicitly create variables for all of the intermediate values.

---

## Comment 25

> Username: wlandry  
> Created_at: 2020-04-04 17:17:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-609060708  

> @wlandry Do you get the proper numerical results with/without your fix?  
  
Yes, but my application is not a good test for correctness.  I use a single precision for all calculations.

---

## Comment 26

> Username: ckormanyos  
> Created_at: 2020-04-04 18:03:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-609067127  

>> Do you think the original PR is a good step in the right direction?  
  
> Yes but we really do need to make the global-default-precision atomic in order to be correct (ie free from data-tearing).  
  
OK. That seems quite reasonable. Thank you.

---

## Comment 27

> Username: ckormanyos  
> Created_at: 2020-04-04 18:09:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-609067803  

>> How many digits are you using in the first place?  
  
> 665 bits, or 200 decimal digits.  
  
>> Are you even changing precision? Or are you just getting traffic at the subroutine that might change precision?  
  
> I never change precision.  
  
Another crazy idea at the cost of your patience (and digressing once again off topic, my bad, sorry), but maybe just to get a few more options out on the table for this or other designs.  
  
Sometimes exactly in that fixed-precision 30...300 decimal digit range, when add, sub, mul, div provide work of the application, it can be interesting or even worthwhile to gove the old `cpp_dec_float` a try. This class can be quite efficient in that particular domain, although not quite so clearly specifiable due to larger guard digit counts and a few other less common quirks.  
  
You might, if your design is flexible regarding big number backend type (as a template parameter to `boost::multiprecision::number`), give it a go with `cpp_dec_float`.

---

## Comment 28

> Username: jzmaddock  
> Created_at: 2020-04-04 18:23:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-609069563  

If memory allocation is really the killer - there is another option which removes it altogether - use mpfr_float at fixed precision and void for the allocator template parameter - all memory will be allocated within the object (ie no dynamic allocations).  The catch is that moves become much more expensive because we have to copy the whole blob of data and not just move a pointer.

---

## Comment 29

> Username: wlandry  
> Created_at: 2020-04-04 21:05:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-609089066  

> Sometimes exactly in that fixed-precision 30...300 decimal digit range, when add, sub, mul, div provide work of the application, it can be interesting or even worthwhile to gove the old `cpp_dec_float` a try. This class can be quite efficient in that particular domain, although not quite so clearly specifiable due to larger guard digit counts and a few other less common quirks.  
  
> If memory allocation is really the killer - there is another option which removes it altogether - use mpfr_float at fixed precision and void for the allocator template parameter - all memory will be allocated within the object (ie no dynamic allocations). The catch is that moves become much more expensive because we have to copy the whole blob of data and not just move a pointer.  
  
The precision has to be specified at run time.  Also, in our tests, GMP's floats are about twice as fast as MPFR's floats.  I have not experimented with cpp_dec_float since there are stern performance warnings in the documentation.  In general, we do need 1280 bits of precision (~400 decimal digits) with some regularity.  
  
But this is all getting off topic.  The goal here is to fix a performance problem in boost::multiprecision, not debug my code ;)

---

## Comment 30

> Username: jzmaddock  
> Created_at: 2020-04-05 08:31:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-609379294  

> But this is all getting off topic. The goal here is to fix a performance problem in boost::multiprecision, not debug my code ;)  
  
Haha, indeed.  
  
@ckormanyos : I wonder if we need a test case / example here?  It should be embarrassingly parallel, access global state (numeric constants for example?), and take enough time to run to get a clear picture of the performance change as each new processor is brought into play.  I wonder if numeric integration would be a good example?  A sophisticated approach would guestimate the area under each section and set the working precision according to how much contribution that sector makes to the total.

---

## Comment 31

> Username: ckormanyos  
> Created_at: 2020-04-05 08:31:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-609379298  

> The goal here is to fix a performance problem in boost::multiprecision  
  
I think we are on track on this goal and the original PR moves toward this.

---

## Comment 32

> Username: ckormanyos  
> Created_at: 2020-04-05 08:35:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-609379760  

> @ckormanyos : I wonder if we need a test case / example here? It should be embarrassingly parallel, access global state (numeric constants for example?), and take enough time to run to get a clear picture of the performance change as each new processor is brought into play. I wonder if numeric integration would be a good example? A sophisticated approach would guestimate the area under each section and set the working precision according to how much contribution that sector makes to the total.  
  
I was thinking the same thing. I will attempt to conjure up an example featuring known numeric values that exercise the aspects that you mention.  
  
As you know, I am doing a lot of parallel stuff right now with fractal imaging. But that does not really have the depth for what you mention.  
  
So we might need to look into a more classical numeric example that you mention. Actually, calculating ascissas and weights would be a candidate. But we will discuss this off site.

---

## Comment 33

> Username: jzmaddock  
> Created_at: 2020-04-05 12:52:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-609411775  

I've just merged the PR that includes this, plus makes the default precision atomic, should be fixed now.

---

## Comment 34

> Username: ckormanyos  
> Created_at: 2020-04-05 20:56:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/211#issuecomment-609481288  

>> @ckormanyos : I wonder if we need a test case / example here?  
  
> will attempt to conjure up an example featuring known numeric values   
  
See #213

---
