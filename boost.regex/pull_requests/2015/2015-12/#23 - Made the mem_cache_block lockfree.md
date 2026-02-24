# #23 Made the mem_cache_block lockfree [Merged]

> Username: ylow  
> Created at: 2015-12-09 05:19:41 UTC  
> Updated at: 2016-01-10 18:45:37 UTC  
> Merged at: 2016-01-10 18:45:07 UTC  
> Closed at: 2016-01-10 18:45:07 UTC  
> Url: https://github.com/boostorg/regex/pull/23  

This _significantly_ improves parallel performance of regex.  
Currently if I have a large number of threads all using regexes; even if  
they are using idependent regex objects, performance is still extremely poor  
due to the lock inside of the mem_block_cache.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-12-09 18:23:45 UTC  
> Url: https://github.com/boostorg/regex/pull/23#issuecomment-163348722  

This is a good idea, but the patch isn't completely portable just yet.  For preference I would like a solution which:  
- Uses <atomic> when BOOST_NO_CXX11_HDR_ATOMIC is not set.  The code will then need to check ATOMIC_POINTER_LOCK_FREE == 2 before using atomic pointers, otherwise:  
- Uses Boost.Atomic, but checks BOOST_ATOMIC_POINTER_LOCK_FREE==2 before attempting to use atomics.  Otherwise:  
- Falls back to the original implementation.

---

## Comment 2

> Username: ylow  
> Created_at: 2015-12-11 08:38:43 UTC  
> Url: https://github.com/boostorg/regex/pull/23#issuecomment-163875447  

sounds good. I will update the pull request.

---

## Comment 3

> Username: ylow  
> Created_at: 2015-12-15 22:30:34 UTC  
> Url: https://github.com/boostorg/regex/pull/23#issuecomment-164917697  

I have updated with the suggested checks.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2015-12-16 18:55:28 UTC  
> Updated_at: 2015-12-31 23:48:07 UTC  
> Url: https://github.com/boostorg/regex/pull/23#discussion_r47817228  

This include is still needed when neither <atomic> not <boost/atomic.hpp> provide the functionality we require.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2015-12-16 18:56:26 UTC  
> Updated_at: 2015-12-31 23:48:07 UTC  
> Url: https://github.com/boostorg/regex/pull/23#discussion_r47817345  

This include should be inside the #ifdef BOOST_NO_CXX11_HDR_ATOMIC block to avoid unnecessary inclusion.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2015-12-16 19:16:16 UTC  
> Updated_at: 2015-12-31 23:48:07 UTC  
> Url: https://github.com/boostorg/regex/pull/23#discussion_r47820209  

We have a problem here: if a regex object is constructed and used at program startup then mem_block_cache may be used uninitialised (since order of initialization is undefined).   For std::atomic we can statically initialize, as with the old code, for boost:atomic we can't unfortunately :(  
  
Only workaround I can think of at present is to replace the global variable with a function returning a reference to the cache:  
  
mem_block_cache& get_cache()  
{  
   static mem_block_cache cache;  
   return cache;  
}  
  
Which would also require changing a lot of other code, and making sure the above function is called at program startup to avoid race-conditions when it is eventually called.  
  
There is another issue with boost:atomic, a default initialized atomic as in your code above, actually leaves the value uninitialised, not zero initialized as we require (checked on VC14).  Which complicates things even more!  I'm not sure what to do about this at present, any thoughts?

---

## Comment 7

> Username: ylow  
> Created_at: 2015-12-16 19:27:52 UTC  
> Updated_at: 2015-12-31 23:48:07 UTC  
> Url: https://github.com/boostorg/regex/pull/23#discussion_r47821807  

Hmm.... That's messy. A possibility is this:  
  
Change the constructor of mem_block_cache to a member function, say... initialize()  
  
Add a std::once_flag to the mem_block_cache object.  
  
In the get() and put() function use std::call_once to call initialize()  
  
Assuming std::call_once / boost::call_once has a well implemented fast-path, I think this should work and maintain performance.

---

## Comment 8

> Username: ylow  
> Created_at: 2015-12-16 19:40:04 UTC  
> Updated_at: 2015-12-31 23:48:07 UTC  
> Url: https://github.com/boostorg/regex/pull/23#discussion_r47823380  

Is there a header that should be included for BOOST_NO_CXX11_HDR_ATOMIC ?

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2015-12-17 10:22:43 UTC  
> Updated_at: 2015-12-31 23:48:07 UTC  
> Url: https://github.com/boostorg/regex/pull/23#discussion_r47889761  

> Is there a header that should be included for BOOST_NO_CXX11_HDR_ATOMIC ?  
  
boost/config.hpp - it's already included.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2015-12-17 10:26:03 UTC  
> Updated_at: 2015-12-31 23:48:07 UTC  
> Url: https://github.com/boostorg/regex/pull/23#discussion_r47890108  

On 16/12/2015 19:37, Yucheng Low wrote:  
  
> In src/regex.cpp   
> https://github.com/boostorg/regex/pull/23#discussion_r47821807:  
>   
> > @@ -191,7 +191,9 @@ BOOST_REGEX_DECL void BOOST_REGEX_CALL put_mem_block(void\* p)  
> >   
> >  #else  
> >   
> > -#ifdef BOOST_HAS_THREADS  
> > +#if defined(BOOST_REGEX_MEM_BLOCK_CACHE_LOCK_FREE)  
> > +mem_block_cache block_cache;  
>   
> Hmm.... That's messy. A possibility is this:  
>   
> Change the constructor of mem_block_cache to a member function, say...   
> initialize()  
>   
> Add a std::once_flag to the mem_block_cache object.  
>   
> In the get() and put() function use std::call_once to call initialize()  
>   
> Assuming std::call_once / boost::call_once has a well implemented   
> fast-path, I think this should work and maintain performance.  
  
That would work, and would only be needed for boost::atomic as   
std::atomic can be statically initialized.  Would have to use   
boost::call_once though, which brings in a dependency on Boost.Thread   
which I'd rather avoid if possible.  
  
Since most current compilers have <atomic> is it sufficient to just   
switch between std::atomic or else the old mutex code and leave   
boost::atomic out of it altogether?

---

## Comment 11

> Username: ylow  
> Created_at: 2015-12-17 16:18:31 UTC  
> Updated_at: 2015-12-31 23:48:07 UTC  
> Url: https://github.com/boostorg/regex/pull/23#discussion_r47925056  

Yeah. that might be the simplest option for now. I will replace all boost::atomic with just std::atomic and static initialize that.

---

## Comment 12

> Username: ylow  
> Created_at: 2015-12-31 23:48:49 UTC  
> Url: https://github.com/boostorg/regex/pull/23#issuecomment-168263078  

Sorry for the delay. I have updated the pull request with the fixes.

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2016-01-09 16:55:29 UTC  
> Url: https://github.com/boostorg/regex/pull/23#issuecomment-170259544  

> Sorry for the delay. I have updated the pull request with the fixes.  
  
I'm also backlogged, will get to this shortly, John.

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2016-01-10 18:45:37 UTC  
> Url: https://github.com/boostorg/regex/pull/23#issuecomment-170381242  

Many thanks for this!

---
