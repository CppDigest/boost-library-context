# #86 - memory leak in singleton [Closed]

> Username: tobias-loew  
> Created at: 2018-01-17 09:28:22 UTC  
> Updated at: 2018-05-19 09:59:39 UTC  
> Closed at: 2018-05-17 20:56:43 UTC  
> Url: https://github.com/boostorg/serialization/issues/86  

After commit 7d216b47619f6b7aa57c5f0e48b9131a025426f9 (where singleton_wrapper is now allocated on the heap (include/boost/serialization/singleton.hpp:138)) Visual Studio reports memory leaks for those allocated objects.

---

## Comment 1

> Username: tobias-loew  
> Created at: 2018-01-17 11:43:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/86#issuecomment-358280067  

It seems that the destructor of singleton never gets called. IMHO returning the pointer as a reference and binding it to singleton:<T>::m_instance won't call the destructor at exit, since there is no temporary object whose lifetime got expanded.

---

## Comment 2

> Username: res2k  
> Created at: 2018-01-17 11:53:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/86#issuecomment-358282289  

`~singleton()` should delete the instance, so the question is why it isn't called.

---

## Comment 3

> Username: tobias-loew  
> Created at: 2018-01-17 15:26:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/86#issuecomment-358339062  

There are no singleton-instances in my code. IMHO singletons are TYPES with static methods (e.g. get_const_instance() or get_mutable_instance()).  
Before the change everything worked fine.   
And by the way, the singleton destructor won't even compile for types with protected dtors since it doesn't use a singleton_wrapper<T&> static-cast for the delete-call.  
Since nobody complained about that, I assume the singleton-dtor doesn't get called at all. (Also here https://github.com/boostorg/serialization/pull/79 in vasild's comment on Nov 22, 2017 there are 1,544 bytes leaking - and nobody seems to care!)

---

## Comment 4

> Username: gast128  
> Created at: 2018-01-30 08:51:14 UTC  
> Updated at: 2018-01-30 08:52:02 UTC  
> Url: https://github.com/boostorg/serialization/issues/86#issuecomment-361518906  

This is severe. We track memory leaks by looking at Visual Studio output windows after program close and this Boost.Serialization library is currently spamming the whole output window. Even if they are only initialization issues, they mask other memory leak reports.  
  
Even more severely it seems to crash our application now if a DLL's is dynamically unloaded and loaded.

---

## Comment 5

> Username: Flamefire  
> Created at: 2018-04-25 15:41:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/86#issuecomment-384333526  

@res2k `~singleton` is not called because `singleton` is never instantiated. See #104

---

## Comment 6

> Username: robertramey  
> Created at: 2018-05-17 20:56:43 UTC  
> Url: https://github.com/boostorg/serialization/issues/86#issuecomment-390008817  

fixed

---

## Comment 7

> Username: Flamefire  
> Created at: 2018-05-19 09:59:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/86#issuecomment-390394179  

Please note that your fix reverts to 1.65.x behaviour causing crashes (see test_dll_exported) and also `is_destroyed` is still dysfunctional (see my test cases). So please do not include it in future Boost releases until #104 is dealt with
