# #102 - Plea to revert recent changes to singleton.hpp [Closed]

> Username: tobias-loew  
> Created at: 2018-03-30 15:16:15 UTC  
> Updated at: 2018-05-17 20:56:00 UTC  
> Closed at: 2018-05-17 20:55:59 UTC  
> Url: https://github.com/boostorg/serialization/issues/102  

This is a plea to revert all the changes recently done to singleton.hpp. The reasons for this plea are  
- having to call destructors for singlons to prevent memory-leaks is very unusual and counter-intuitive  
- using  
       if (!t)  
            t = new singleton_wrapper;  
    in singleton::get_instance() is not thread-safe (the former code using a non-pointer static variable was thread-safe by design)  
- The reason for all those changes is a non-standard-conforning implementation detail of the gcc-compiler. The so-called fix for the gcc-error was not a fix at all, but simple a memory-leak as the problematik pointer was never(!) deleted (and therefore the problem seemde to be fixed).  
If not removed at all, then those changes should definitively be guarded by some gcc-workaround macro, that all other compilers use the old - and totally correct - code.  
  
In my opinion those changes should be reverted as soon as possible and hopefully make it into Boost 1.67.  
  
Tobias

---

## Comment 1

> Username: robertramey  
> Created at: 2018-03-30 18:19:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/102#issuecomment-377588296  

sorry I haven't been able to address this.  The reason is that fixing it properly has to going back and studying why we made the change in the first place.  If I don't do that, then we just end up playing whack-a-mole.  To prevent this, I usually try to get a test case which fails and add it to the test suite.  So the best would be if we had a small program which displays the existence of the memory leak.  Then we'll be sure it never comes back.  Then we have to fix it and test it on all the platforms we use.  That takes a while.  No way that this will be ready for 1.67.  
  
What I recommend is that you either  
a) role back your local copy to 1.65  
b) edit your change into your local copy of 1.66 or 1.67  
  
Hopefully we'll be able to fix this by 1.68

---

## Comment 2

> Username: tobias-loew  
> Created at: 2018-04-11 16:30:36 UTC  
> Updated at: 2018-04-11 16:31:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/102#issuecomment-380514883  

I didn't have time to create  an example yet, but every code using BOOST_CLASS_EXPORT now has a memory-leak: BOOST_CLASS_EXPORT  expands BOOST_CLASS_EXPORT_IMPLEMENT and in there a singleton is instantiated. I haven't completely checked but I strongly doubt that for those instances the destructors ever get called.  
  
#define BOOST_CLASS_EXPORT_IMPLEMENT(T)                      \  
    namespace boost {                                        \  
    namespace archive {                                      \  
    namespace detail {                                       \  
    namespace extra_detail {                                 \  
    template<>                                               \  
    struct init_guid< T > {                                  \  
        static guid_initializer< T > const & g;              \  
    };                                                       \  
    guid_initializer< T > const & init_guid< T >::g =        \  
        ::boost::serialization::singleton<                   \  
            guid_initializer< T >                            \  
        >::get_mutable_instance().export_guid();             \  
    }}}}                                                     \

---

## Comment 3

> Username: tobias-loew  
> Created at: 2018-04-12 06:35:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/102#issuecomment-380694540  

There's also a Boost-bug connected to those changes https://svn.boost.org/trac10/ticket/13186

---

## Comment 4

> Username: Flamefire  
> Created at: 2018-04-25 15:24:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/102#issuecomment-384327549  

I described the problem in detail in #104. See there for hints on how to fix

---

## Comment 5

> Username: robertramey  
> Created at: 2018-05-17 20:55:59 UTC  
> Url: https://github.com/boostorg/serialization/issues/102#issuecomment-390008630  

I've reversed the commit that causes the problem.  It was done in haste.  Since it doesn't use heap memory any more, all such leaks should disappear.  Unfortunately, I still have an issue with test_dll_export which is what this change had "fixed".  On going.
