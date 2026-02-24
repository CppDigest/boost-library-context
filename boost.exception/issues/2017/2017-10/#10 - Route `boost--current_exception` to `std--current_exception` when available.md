# #10 - Route `boost::current_exception` to `std::current_exception` when available? [Closed]

> Username: pdimov  
> Created at: 2017-10-26 00:45:03 UTC  
> Updated at: 2020-02-18 02:59:37 UTC  
> Closed at: 2020-02-18 02:59:36 UTC  
> Url: https://github.com/boostorg/exception/issues/10  

From https://github.com/boostorg/system/pull/18, suggested by @ned14:  
  
> Also, one might consider also rerouting  boost::current_exception()  to  std::current_exception() . The latter is surely always going to be equal or better to the Boost implementation.

---

## Comment 1

> Username: zajo  
> Created at: 2017-10-26 01:39:00 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-339523917  

It is unclear to me who the target audience for such rerouting would be.  
  
If you use `boost::throw_exception()` to throw, then `boost::current_exception() / boost::rethrow_exception()` work as well as `std::current_exception() / std::rethrow_exception()`, but you must use the `boost` versions if you do need your code to work on old compilers; if you don't, you may (should?) use the `std` versions.  
  
If you don't use `boost::throw_exception()` to throw, then you should always use the `std::current_exception() / std::rethrow_exception()`, since your code won't work correctly on old compilers anyway.  
  
Secondly, there are other components in Boost, e.g. `boost::function`, which aren't "rerouted". If such rerouting is needed, it should probably be done uniformly for all such components, though I'm not sure if there are important subtle semantic differences (there probably are.)

---

## Comment 2

> Username: ned14  
> Created at: 2017-10-26 02:36:40 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-339532780  

This rerouting would only apply on C++ 11 or newer, and presumably only on compilers without borked `std::current_exception()`.  
  
I get the argument "why bother rerouting?". But there is also the argument "why not bother rerouting?". Unlike `boost::function`, as far as I am aware, there is zero known behavioural differences between `std::current_exception/rethrow_exception` and `boost::current_exception/rethrow_exception`. Plus, rerouting these keeps symmetry with the rerouting of Boost.System to its `std` equivalents.

---

## Comment 3

> Username: zajo  
> Created at: 2017-10-26 03:10:41 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-339537918  

My question wasn't so much "why", but who would benefit? I understand the motivation, and I'm not against it in principle, but I am worried about possibly breaking client code which currently works fine, and I think that it is important to understand who cares about this change.  
  
The safe approach would be to provide a _new_ configuration macro, e.g. `BOOST_EXCEPTION_USE_STD_EXCEPTION_PTR` (off by default), which would guarantee nothing will break. Would that work? This isn't just paranoia, recently I tried to remove the support for non-intrusive `boost::exception_ptr`, specific for MSVC, thinking that probably nobody uses it any more (since modern compilers have `std::exception_ptr`), but it turned out I was wrong, so that's back in.  
  
(By the way, I am not aware of any differences between `std::function` and `boost::function`, when I changed the allocator support in `boost::function` to match that of `boost::shared_ptr`, I wrote a proposal which made it into the standard as well :). But possibly I'm missing something.)

---

## Comment 4

> Username: zajo  
> Created at: 2017-10-26 03:58:34 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-339544393  

Actually I've realized that there is another problem: the custom error info container in `boost::exception` is held by a `boost::shared_ptr`, and all copies of a `boost::exception` object share that state. Only when making a `boost::exception_ptr`, the copied exception object doesn't share that state with the source, but that copy is _not_ created using the copy constructor. A copy created by `std::current_exception` would share that state, potentially with explosive consequences if the exception is transported to another thread.  
  
While this design is not ideal and can probably be safely changed so `boost::exception` objects never share state, this would not be a trivial change.

---

## Comment 5

> Username: jeking3  
> Created at: 2018-11-12 22:01:16 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-438043599  

This discussion stalled, was there a conclusion (for example, since it was not trivial, will we do it?)

---

## Comment 6

> Username: zajo  
> Created at: 2018-11-14 00:51:28 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-438495880  

I don't know if this can be fixed. I'll try to explain simpler:  
  
A `boost::exception` object holds a map of data, through a custom intrusive refcounting smart pointer. Copying `exception` objects within a thread only changes the refcount.  
  
But that map is not thread-safe, so when a `boost::exception` object is transported to another thread, we must create a copy of the map, which `boost::current_exception` knows and does, but `std::current_exception` would simply invoke the copy constructor, which won't copy the map.  
  
Maybe it can be done with a move constructor, but this would require `#ifdef`ing, since I don't think it's a good idea to break C++03 support.  
  
I'll close this for now, if anyone has an idea for a workaround, feel free to reopen and continue the discussion.

---

## Comment 7

> Username: dariomt  
> Created at: 2019-02-22 09:45:37 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-466337982  

I have a suggestion (that would solve my use case):  
1) first have boost::current_exception() do its current work, and recover the exception_ptr  
2) then, if this 'failed' (and we would get an exception_ptr pointing to an unknown_exception), reroute to std::current_exception() if supported  
  
This would:  
a) keep existing code using Boost.Exception facilities working correctly  
b) make code *not* using Boost.Exception to throw, work correctly, as if std::current_exception was used  
  
For reference, in my use case I need to   
- use boost::future (which uses boost::current_exception() to transport exceptions across threads)   
- but I cannot use Boost.Exception facilities to throw exceptions (third party code throwing)  
  
Does this make sense?

---

## Comment 8

> Username: zajo  
> Created at: 2019-03-03 06:25:21 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-468993302  

Reroute to std::current_exception, how? std::exception_ptr and boost::exception_ptr are different types.

---

## Comment 9

> Username: dariomt  
> Created at: 2019-03-05 14:56:22 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-469710562  

Hum... maybe wrap the std::exception_ptr in current_exception and unwrap it in rethrow_exception?  
  
In boost::exception_detail::current_exception_impl() (around boost/exception/detail/exception_ptr.hpp:424 in boost 1.64)  
```  
catch(...)  
{  
   try {  
        // wrap the std::exception_ptr in a clone-enabled Boost.Exception object  
       return exception_ptr(shared_ptr<exception_detail::clone_base const>(  
              boost::enable_current_exception(std::current_exception()).clone()));  
    }  
   catch(...) {  
      return exception_detail::current_exception_unknown_exception();  
   }  
}  
```  
  
In boost::rethrow_exception():  
```  
   try {  
        p.ptr_->rethrow();  
    } catch (const std::exception_ptr& ep) {  
            std::rethrow_exception(ep);  
   }  
```  
  
This (pseudo)code is for exposition only. Also, for simplicity this code assumes std::current_exception is available. I could provide a PR if necessary, but I doubt I can get all the #ifdef-ing right!  
  
PS: I took inspiration from https://stackoverflow.com/questions/22010388/converting-stdexception-ptr-to-boostexception-ptr/22881981

---

## Comment 10

> Username: dariomt  
> Created at: 2019-03-05 15:23:41 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-469721552  

I'm testing the above locally and it looks like it works. Two caveats, though:  
  
1) clone is private so we need a reference to the base class  
```  
exception_detail::clone_base const& base {  
    boost::enable_current_exception(std::current_exception())};  
return exception_ptr(shared_ptr<exception_detail::clone_base const>(base.clone()));  
```  
2) the catch(std::exception&) (that redirects to current_exception_unknown_std_exception()) above the catch all clause, is intercepting my user defined exception type (which inherits from std::exception) so I had to remove it for this to work. Not sure if that breaks anything, as the std::current_exception should transport that case correctly.  
  
What do you think?

---

## Comment 11

> Username: zajo  
> Created at: 2019-03-17 05:34:50 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-473618264  

Oh I see, your idea is to use `std::exception_ptr` as a way to be able to clone any exception properly, but still returning a `boost::exception_ptr`. I had misunderstood, I thought you wanted to go the other way around, to be able to effectively make `boost::exception_ptr` be `std::exception_ptr` where available.  
  
This is a great idea, thank you. Since you said you've tested, I'm assuming you can send a pull request?

---

## Comment 12

> Username: dariomt  
> Created at: 2019-03-19 11:00:00 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-474306763  

I'm testing locally against Boost 1.64.0. I guess you'd like the PR against devel branch?  
  
Also, do you know which Boost preprocessor macro can I use to detect support for `std::exception_ptr`?

---

## Comment 13

> Username: zajo  
> Created at: 2019-03-21 00:31:48 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-475078721  

There is no config macro in Boost to detect `std::exception_ptr` support. Probably just check for C++11 support (e.g. `#if __cplusplus > 199711L`) and assume `std::exception_ptr` is available.  
  
You can send a diff with Boost 1.64.0 if that's easier. I'll review and commit manually. Thank you!

---

## Comment 14

> Username: dariomt  
> Created at: 2019-03-28 09:40:18 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-477520964  

My main compiler is VS2015, so I'm afraid `__cplusplus` does not work for me: for some reason it's still defined to be `199711L`.  
I'm looking at Boost.Config and I can't find anything suitable.  
Maybe I can just use `BOOST_NO_CXX11_NOEXCEPT`, and assume that if `noexcept` is supported, then `std::current_exception` is also supported?  
Or maybe I should bring this up in the mailing list?

---

## Comment 15

> Username: zajo  
> Created at: 2019-04-09 18:05:55 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-481367305  

It can't hurt to talk on the mailing list. But it may be safe to assume `exception_ptr` is supported if `__cplusplus` is greater than `199711L`.

---

## Comment 16

> Username: dmenendez-gruposantander  
> Created at: 2019-05-27 09:44:24 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-496151912  

I'm working to provide a PR over develop branch.  
  
BTW, my boss gave me green light to do this task using company time, so I'll do this with my corporatey github account, just to clarify I'm not doing this in my own free time.

---

## Comment 17

> Username: dmenendez-gruposantander  
> Created at: 2019-05-27 16:21:51 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-496259661  

About the docs, should I update the html files under doc/ directly?  
I thought there would be some other source from which the html files are generated...

---

## Comment 18

> Username: dmenendez-gruposantander  
> Created at: 2019-07-01 08:53:49 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-507177233  

The changes (code, tests and docs) are ready in my fork (https://github.com/dmenendez-gruposantander/exception).  
However I have a dependency on Boost.Config PR boostorg/config#285 being merged to develop (for BOOST_NO_CXX11_HDR_EXCEPTION).  
Which is stalled due to CI testing being borked right now :(  
I'll create a PR as soon as #285 is merged to develop.

---

## Comment 19

> Username: zajo  
> Created at: 2019-07-09 20:54:07 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-509803576  

Yes, let's wait until the config change gets merged, and then I'll look at the PR. Thank you!

---

## Comment 20

> Username: dmenendez-gruposantander  
> Created at: 2019-08-28 08:32:42 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-525641575  

Now that the Boost.Config changes have been merged to develop (thanks @jzmaddock!) I've created PR #24 with my changes, so let the code review begin!  
  
Unfortunately, some CI tests are unexpectedly failing, and I'm having a hard time deciphering the errors...  
For some versions of gcc it seems confused about instantiating abstract class clone_base (which I'd say I'm not) and the clang tests I don't think are even related to my changes!  
  
Any help with this is appreciated, as I am being optimistic and was hoping to get this merged in time for the 1.72 release.

---

## Comment 21

> Username: pdimov  
> Created at: 2019-08-28 09:52:17 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-525670818  

The clang errors are because Travis switched to Xenial by default; `dist: trusty` needs to be added to `.travis.yml` as the old clang versions don't work on Xenial.

---

## Comment 22

> Username: dmenendez-gruposantander  
> Created at: 2019-08-29 10:03:29 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-526117695  

Hum... This is not something I should fix in my PR, is it?

---

## Comment 23

> Username: dmenendez-gruposantander  
> Created at: 2019-09-17 15:16:23 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-532267902  

Is there something I can do to help with this issue?  
Not that I'm familiar with the CI setting, but if all it takes is "`dist: trusty` needs to be added to .travis.yml" then I could... create a PR for that?

---

## Comment 24

> Username: dmenendez-gruposantander  
> Created at: 2019-10-09 08:10:44 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-539889773  

Hi, I'm sorry to be nagging about this, but I don't see how I can get this PR merged...  
Is the CI config already fixed?  
How do I "retry" the PR?  
I'd really like to get this merged for 1.72, please.

---

## Comment 25

> Username: dmenendez-gruposantander  
> Created at: 2019-10-18 11:10:32 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-543678933  

OK, I added `dist: trusty` in my PR and the clang problems are solved.  
  
However I still have problem with old versions of gcc which get confused when I bind a `exception_detail::clone_base const&` to the temporary coming out of `boost::enable_current_exception()`.   
Somehow gcc thinks I want a copy!  
Ideas?  
  
I tried `const auto&` but the actual type returned from `boost::enable_current_exception()` has `clone()` as a private member function!

---

## Comment 26

> Username: dmenendez-gruposantander  
> Created at: 2019-10-18 14:18:26 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-543768400  

At last! All tests are green!  
Could you review the proposed changes for this PR please?  
  
PS: the gcc problem was due to initializing the reference with braces instead of using =  
go figure! *slaps face*

---

## Comment 27

> Username: zajo  
> Created at: 2019-10-25 23:55:29 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-546547113  

Yes, I'll look at it. Thank you!

---

## Comment 28

> Username: dmenendez-gruposantander  
> Created at: 2020-01-28 14:28:35 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-579271510  

Any chance to get this into the 1.73 release?  
Is there anything else I can help with?

---

## Comment 29

> Username: dmenendez-gruposantander  
> Created at: 2020-01-31 08:39:23 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-580639001  

In my last (and first) contribution to Boost I was asked about Copyright.  
I'm never sure about how to proceed with this, if it should be part of the PR or where it should appear.  
  
This is it, in case it's necessary:  
Copyright 2019 Dario Menendez, Banco Santander

---

## Comment 30

> Username: zajo  
> Created at: 2020-02-18 02:59:36 UTC  
> Url: https://github.com/boostorg/exception/issues/10#issuecomment-587250382  

Done.
