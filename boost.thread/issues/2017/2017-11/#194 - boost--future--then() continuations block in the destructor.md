# #194 - boost::future::then() continuations block in the destructor [Closed]

> Username: muggenhor  
> Created at: 2017-11-30 12:58:28 UTC  
> Updated at: 2018-02-26 15:58:55 UTC  
> Closed at: 2018-02-23 16:51:04 UTC  
> Url: https://github.com/boostorg/thread/issues/194  

The futures returned from `boost::future<T>::then()` block in the destructor. While this appears to be a design decision to be similar to `boost::async`/`std::async`, it practically makes fire and forget continuations (technically register and forget) impractical.  
  
I'm using these to have the last continuation on a chain fire off an event to Qt to handle on its main loop, this gives me no location where calling .get() is desirable, nor a "natural" location to store the future. So currently I have to resort to keeping a vector of pending futures and occasionally garbage collecting them: `v.erase(std::remove_if(v.begin(), v.end(), [](const auto& f) { return f.is_ready(); }), v.end())`.  
  
I assume that it's obvious how cumbersome and undesirable this is.  
  
I would very much like for futures to never block in the destructor. Preferably for those started from `boost::async` too, but I hardly use that anyway, so only not blocking for those returned from `then()` would be fine for me too.  
  
Alternatively, providing a `detach()` method, like [`stlab::future::detach()`](http://stlab.cc/libraries/concurrency/future/future/detach.html) would be fine too and in line with `thread`'s `detach` method.

---

## Comment 1

> Username: viboes  
> Created at: 2017-11-30 18:03:29 UTC  
> Url: https://github.com/boostorg/thread/issues/194#issuecomment-348270765  

I'm aware of the issue. Boost.thread did it by design following std::async and the first proposals of std::future::then.   
  
The problem is that changing the behavior (even if it is not desired in some contexts) would be a breaking change. I would need to crate a new version, but Idon't want to create a new version using compiler flags as this has become a nightmare to maintain.  
  
So I will need to create a boost/thread_v5 which will produce different binaries lib_boost_thread_v5.a. Boost.Threads is not structured this way, and this will mean a lot of work.  
This is the single way to avoid breaking changes, but before doing that I would like to enumerate all the breaking changes that should be on this new version.  
  
Boost.Thread is an old library, and we have a lot of limitations with C++98. If I moved to a new version, I would like to get rid of C++98 compatibility   
  
We could start a new branch that requires at least C++11, but then we have just access to `std::thread` and `std::future`. So what would be the interest of of Boost.Thread?  
  
* thread interruptions?  I don't like the design of the interruptions and there are now C++ standard proposals for a quite different design for thread interruptions.  
* future continuations? The committee is going in a different direction now on what concerns executors and continuations.  
   
So, in summary, I would accept a PR that do the change non blocking futures with conditional compilation, but I will not fix it mysellf using conditional compilation.   
I would accept also a PR that provides a `detach` member function, as this is a non breaking change   
  
stlab::future has a very different design and interface, and maybe this design could be the one of the new standard futures.  
Is a valid alternative for you to use stlab::future ?

---

## Comment 2

> Username: viboes  
> Created at: 2017-11-30 18:20:31 UTC  
> Url: https://github.com/boostorg/thread/issues/194#issuecomment-348275486  

Now, I remember. I started to implement the blocking policy, but I don't think it works well yet. See BOOST_THREAD_FUTURE_BLOCKING.

---

## Comment 3

> Username: muggenhor  
> Created at: 2017-11-30 19:20:55 UTC  
> Url: https://github.com/boostorg/thread/issues/194#issuecomment-348292602  

> Is a valid alternative for you to use stlab::future ?  
  
Personally: yes. But there's company politics involved, which I can completely side step, because Boost.Thread is already used as an accepted third party library. So:  
> I would accept also a PR that provides a detach member function, as this is a non breaking change  
  
I'll look into doing the work for this. The trick is doing it without affecting behaviour when `.detach()` isn't called, `BOOST_THREAD_FUTURE_BLOCKING` seems to provide some good hints.

---

## Comment 4

> Username: muggenhor  
> Created at: 2018-02-23 16:50:52 UTC  
> Url: https://github.com/boostorg/thread/issues/194#issuecomment-368067238  

After looking at this further it seems that the behavior I get _without_ `#defining BOOST_THREAD_FUTURE_BLOCKING` is exactly what's needed for my use case: future's don't block in the destructor, nor do they cancel execution (similar to what `stlab::future` does when you call `.detach()` on it).
