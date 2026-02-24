# #61 Move Oracle workaround to correct location [Merged]

> Username: jzmaddock  
> Created at: 2015-08-20 09:51:31 UTC  
> Updated at: 2015-08-20 22:00:38 UTC  
> Merged at: 2015-08-20 22:00:38 UTC  
> Closed at: 2015-08-20 22:00:38 UTC  
> Url: https://github.com/boostorg/thread/pull/61  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-08-20 09:52:34 UTC  
> Url: https://github.com/boostorg/thread/pull/61#issuecomment-132958725  

My apologies, previous PR had the #if on the wrong line - which I guess is what happens when you're cutting and pasting between virtual machines and a web interface :(

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2015-08-20 10:31:34 UTC  
> Url: https://github.com/boostorg/thread/pull/61#issuecomment-132967822  

There are also similar failures in calls to invoke when building Boost.Context:  
  
sun.compile.c++ bin.v2/libs/context/build/sun-s11/release/address-model-64/thre  
ading-multi/posix/stack_traits.o  
"./boost/thread/pthread/once_atomic.hpp", line 168: Error: Overloading ambiguity between "boost::detail::invoke<void, void(*)(rlimit*), rlimit*>(void(_)(rlimit_)&, rlimit_)" and "boost::detail::invoke<void, void(_)(rlimit_), rlimit_&>(void(_)(rlimit_)&, rlimit_&)".  
"libs/context/src/posix/stack_traits.cpp", line 66:     Where: While instantiating "boost::call_once<void(_)(rlimit_), rlimit_>(boost::once_flag&, void(_)(rlimit_), rlimit*)".  
"libs/context/src/posix/stack_traits.cpp", line 66:     Where: Instantiated from non-template code.  
  
But those I can't see how to easily solve :(

---

## Comment 3

> Username: viboes  
> Created_at: 2015-08-20 22:00:33 UTC  
> Url: https://github.com/boostorg/thread/pull/61#issuecomment-133190760  

Thanks for the patches

---
